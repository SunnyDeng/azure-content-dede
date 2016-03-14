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
   ms.date="02/29/2016"
   ms.author="andkjell"/>

# Azure AD Connect: Versionsveröffentlichungsverlauf

Das Azure Active Directory-Team aktualisiert Azure AD Connect regelmäßig mit neuen Features und Funktionen. Nicht alle Erweiterungen gelten für alle Benutzergruppen.

Dieser Artikel soll Ihnen helfen, die Versionen zu verfolgen, die veröffentlicht wurden, und zu wissen, ob Sie auf die neueste Version aktualisieren müssen oder nicht.

Verwandte Links:

- Verschiedene Methoden zum [Aktualisieren von einer früheren Version auf die aktuelle Version](active-directory-aadconnect-upgrade-previous-version.md) von Azure AD Connect.
- Die zum Anwenden eines Updates erforderlichen Berechtigungen sind unter [Konten und Berechtigungen](active-directory-aadconnect-accounts-permissions.md#upgrade) aufgeführt.
- [Azure AD Connect herunterladen](http://go.microsoft.com/fwlink/?LinkId=615771)

## 1\.1.110.0
Veröffentlicht im Februar 2016

**Behobene Probleme:**

- Das Upgrade von früheren Versionen kann nicht ausgeführt werden, wenn die Installation nicht im Standardordner **C:\\Programme** durchgeführt wird.
- Wenn Sie bei der Installation am Ende des Installations-Assistenten die Option **Starten Sie den Synchronisierungsvorgang ...** deaktivieren, wird der Planer durch erneutes Ausführen des Installations-Assistenten nicht aktiviert.
- Auf Servern, auf denen das Datums-/Uhrzeitformat nicht dem US-englischen Format entspricht, wird der Planer nicht wie erwartet ausgeführt. Außerdem wird verhindert, dass `Get-ADSyncScheduler` korrekte Zeitangaben zurückgibt.
- Wenn Sie eine frühere Version von Azure AD Connect mit AD FS als Anmeldeoption und Upgrade installiert haben, können Sie den Installations-Assistenten nicht erneut ausführen.

## 1\.1.105.0
Veröffentlicht im Februar 2016

**Neue Features:**

- [Automatisches Upgradefeature](active-directory-aadconnect-feature-automatic-upgrade.md) für Kunden mit Expresseinstellungen.
- Unterstützung für den globalen Administrator mit MFA und PIM im Installations-Assistenten.
    - Sie müssen für Ihren Proxy festlegen, dass auch Datenverkehr für https://secure.aadcdn.microsoftonline-p.com zulässig ist, wenn Sie MFA verwenden.
    - Sie müssen der Liste vertrauenswürdiger Websites https://secure.aadcdn.microsoftonline-p.com hinzufügen, damit MFA ordnungsgemäß funktioniert.
- Das Ändern der Anmeldemethode des Benutzers nach der Erstinstallation ist zulässig.
- Die [Filterung von Domänen und Organisationseinheiten](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) im Installations-Assistenten ist möglich. Dadurch wird außerdem das Herstellen einer Verbindung mit Gesamtstrukturen ermöglicht, in denen nicht alle Domänen verfügbar sind.
- Der [Planer](active-directory-aadconnectsync-feature-scheduler.md) ist in das Synchronisierungsmodul integriert.

**Features, die von der Vorschau auf die allgemeine Verfügbarkeit hochgestuft wurden:**

- [Geräterückschreiben](active-directory-aadconnect-feature-device-writeback.md)
- [Verzeichniserweiterungen](active-directory-aadconnectsync-feature-directory-extensions.md)

**Neue Vorschaufeatures:**

- Das neue Standardintervall für den Synchronisierungszyklus beträgt 30 Minuten. In allen früheren Versionen wurde ein Intervall von 3 Stunden verwendet. Unterstützung zum Ändern des [Planer](active-directory-aadconnectsync-feature-scheduler.md)-Verhaltens wurde hinzugefügt.

**Behobene Probleme:**

- Von der Seite zum Überprüfen von DNS-Domänen wurden die Domänen nicht immer erkannt.
- Domänen-Admin-Anmeldeinformationen werden beim Konfigurieren von AD FS abgefragt.
- Die lokalen AD-Konten werden vom Installations-Assistenten nicht erkannt, wenn sie sich in einer Domäne befinden, die eine andere DNS-Struktur als die Stammdomäne aufweist.

## 1\.0.9131.0
Veröffentlicht im Dezember 2015

**Behobene Probleme:**

- Möglicherweise funktioniert die Kennwortsynchronisierung in AD DS nicht beim Ändern von Kennwörtern, jedoch beim Festlegen eines Kennworts.
- Mit einem Proxy-Server schlägt die Azure AD-Authentifizierung während der Installation möglicherweise fehl oder die Aktualisierung der Konfigurationsseite wird aufgehoben.
- Das Aktualisieren einer vorherigen Version von Azure AD Connect mit einem vollständigen SQL Server schlägt fehl, wenn Sie in SQL nicht SA sind.
- Beim Aktualisieren einer vorherigen Version von Azure AD Connect über einen Remotecomputer mit SQL Server wird die Fehlermeldung "Kein Zugriff auf die ADSync SQL-Datenbank." angezeigt.

## 1\.0.9125.0
Veröffentlicht im November 2015

**Neue Features:**

- Es ist möglich, die Vertrauensstellung zwischen AD FS und Azure AD neu zu konfigurieren.
- Das Active Directory-Schema kann aktualisiert und die Synchronisierungsregeln können neu generiert werden.
- Synchronisierungsregeln können deaktiviert werden.
- In einer Synchronisierungsregel kann „AuthoritativeNull“ als neues Literal definiert werden.

**Neue Vorschaufeatures:**

- [Azure AD Connect Health für die Synchronisierung](active-directory-aadconnect-health-sync.md)
- Unterstützung für die Kennwortsynchronisierung mit [Azure AD-Domänendiensten](active-directory-get-started.md)

**Neues unterstütztes Szenario:**

- Es werden mehrere lokale Exchange-Organisationen unterstützt. Weitere Informationen finden Sie unter [Hybrid-Bereitstellungen mit mehreren Active Directory-Gesamtstrukturen](https://technet.microsoft.com/library/jj873754.aspx).

**Behobene Probleme:**

- Probleme bei der Kennwortsynchronisierung:
    - Für ein Objekt, das sich bisher außerhalb des Bereichs befand und das dann in den Bereich verschoben wurde, wird das Kennwort nicht synchronisiert. Dies schließt die Organisationseinheit und die Attributfilterung ein.
    - Wenn eine neue Organisationseinheit in die Synchronisierung einbezogen wird, ist keine vollständige Kennwortsynchronisierung erforderlich.
    - Nach dem Aktivieren eines bisher deaktivierten Benutzers wird das Kennwort nicht synchronisiert.
    - Für die Warteschlange mit Kennwortwiederholungsversuchen gilt kein Limit, der bisherige Grenzwert von 5.000 Objekten bis zum Außerkraftsetzen wurde entfernt.
    - [Verbesserte Problembehandlung](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).
- Mit der Gesamtstrukturfunktionsebene von Windows Server 2016 kann keine Verbindung mit Active Directory hergestellt werden.
- Nach der Erstinstallation kann die für das gruppenspezifische Filtern verwendete Gruppe nicht geändert werden.
- Wenn Benutzer, für die das Kennwortrückschreiben aktiviert ist, ihr Kennwort ändern, wird auf dem Azure AD Connect-Server kein neues Benutzerprofil mehr angelegt.
- In den Geltungsbereichen der Synchronisierungsregeln können keine langen Ganzzahlen als Werte verwendet werden.
- Das Kontrollkästchen „Geräterückschreiben“ bleibt deaktiviert, wenn nicht erreichbare Domänencontroller vorhanden sind.

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
- Beim Verwenden der Kennwortzurücksetzung wird die CPU übermäßig ausgelastet.

**Entfernte Vorschaufunktionen:**

- Die Vorschaufunktion [Benutzerrückschreiben](active-directory-aadconnect-feature-preview.md#user-writeback) wurde aufgrund von Rückmeldungen unserer Vorschaukunden vorübergehend entfernt. Sie wird später erneut hinzugefügt werden, wenn das bereitgestellte Feedback umgesetzt wurde.

## 1\.0.8641.0
Veröffentlicht im Juni 2015

**Erste Version von Azure AD Connect.**

Änderung des Namens von Azure AD Sync zu Azure AD Connect.

**Neue Features:**

- [Installation mit Express-Einstellungen](active-directory-aadconnect-get-started-express.md)
- Funktion [ADFS konfigurieren](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
- Funktion [Von DirSync aktualisieren](active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [Verhindern von versehentlichen Löschungen](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- Neuer [Stagingmodus](active-directory-aadconnectsync-operations.md#staging-mode)

**Neue Vorschaufeatures:**

- [Rückschreiben von Benutzern](active-directory-aadconnect-feature-preview.md#user-writeback)
- [Gruppenrückschreiben](active-directory-aadconnect-feature-preview.md#group-writeback)
- [Geräterückschreiben](active-directory-aadconnect-feature-device-writeback.md)
- [Verzeichniserweiterungen](active-directory-aadconnect-feature-preview.md#directory-extensions)


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

- Die Synchronisierung von Kennwörtern mit attributbasierter Filterung wird jetzt unterstützt. Weitere Informationen finden Sie unter [Synchronisierung von Kennwörtern mit Filterung](active-directory-aadconnectsync-configure-filtering.md).
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

## Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0302_2016-->