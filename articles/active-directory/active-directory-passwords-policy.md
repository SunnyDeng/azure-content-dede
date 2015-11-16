<properties
	pageTitle="Kennwortrichtlinien und -einschränkungen in Azure Active Directory | Microsoft Azure"
	description="Beschreibt die Richtlinien, die in Azure Active Directory für Kennwörter gelten, einschließlich der zulässigen Zeichen, der Länge und dem Ablauf."
  services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="curtand"/>


# Kennwortrichtlinien und -einschränkungen in Azure Active Directory

Dieser Artikel beschreibt die Kennwortrichtlinien und Komplexitätsanforderungen im Zusammenhang mit den in Ihrem Azure AD-Verzeichnis gespeicherten Benutzerkonten.

## UserPrincipalName-Richtlinien, die für alle Benutzerkonten gelten

Jedes Benutzerkonto, das sich beim Azure AD-Authentifizierungssystem anmeldet, muss über ein eindeutiges, diesem Konto zugeordnetes Benutzerprinzipalnamenattribut (UPN, User Principal Name) verfügen. Die folgende Tabelle zeigt die Richtlinien, die sowohl für lokale Active Directory-Benutzerkonten (mit Cloudsynchronisierung) als auch für Benutzerkonten gelten, die ausschließlich in der Cloud gespeichert werden.

| Eigenschaft | UserPrincipalName-Richtlinien |
|   ----------------------- |   ----------------------- |
| Zulässige Zeichen | <ul> <li>A – Z</li> <li>a -z </li><li>0 – 9</li> <li> . - \_ ! # ^ ~</li></ul> |
| Unzulässige Zeichen | <ul> <li>@</li> <li>Darf keinen Punkt (.) unmittelbar vor dem @-Symbol enthalten.</li></ul> |
| Längenbeschränkungen | <ul> <li>Die Gesamtlänge darf 113 Zeichen nicht überschreiten.</li><li>64 Zeichen vor dem @-Symbol</li><li>48 Zeichen nach dem @-Symbol</li></ul>

## Kennwortrichtlinien, die nur für Cloudbenutzerkonten gelten

Die folgende Tabelle beschreibt die verfügbaren Kennwortrichtlinieneinstellungen, die auf die in Azure AD erstellten und verwalteten Benutzerkonten angewendet werden können.

| Eigenschaft | Anforderungen |
|   ----------------------- |   ----------------------- |
| Zulässige Zeichen | <ul><li>A – Z</li><li>a -z </li><li>0 – 9</li> <li>@ # $ % ^ & * - \_ ! + = [ ] { } | \\ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Unzulässige Zeichen | <ul><li>Unicode-Zeichen</li><li>Leerzeichen</li><li>Leerzeichen</li><li> **Nur für sichere Kennwörter**: Dürfen keinen Punkt (.) unmittelbar vor dem @-Symbol enthalten</li></ul> |
| Kennworteinschränkungen | <ul><li>Mindestens 8 Zeichen und höchstens 16 Zeichen</li><li>**Nur für sichere Kennwörter**: Es müssen 3 von 4 der folgenden Zeichenarten enthalten sein:<ul><li>Kleinbuchstaben</li><li>Großbuchstaben</li><li>Zahlen (0-9)</li><li>Symbole (siehe Kennworteinschränkungen oben)</li></ul></li></ul> |
| Zeitraum bis zum Ablauf des Kennworts | <ul><li>Standardwert: **90** Tage </li><li>Der Wert kann im Azure Active Directory-Modul für Windows PowerShell mit dem Cmdlet „Set-MsolPasswordPolicy“ konfiguriert werden.</li></ul> |
| Benachrichtigung bei Ablauf des Kennworts | <ul><li>Standardwert: **14** Tage (vor Ablauf des Kennworts)</li><li>Der Wert kann mit dem Cmdlet „Set-MsolPasswordPolicy“ konfiguriert werden.</li></ul> |
| Ablauf des Kennworts | <ul><li>Standardwert: **false** days (zeigt an, dass der Kennwortablauf aktiviert ist) </li><li>Der Wert kann mit dem Cmdlet „Set-MsolUser“ für einzelne Benutzerkonten konfiguriert werden. </li></ul> |
| Kennwortverlauf | Das letzte Kennwort kann nicht erneut verwendet werden. |
| Zeitraum für Kennwortverlauf | Endlos |
| Kontosperrung | <ul><li>Nach 10 erfolglosen Anmeldeversuchen (falsches Kennwort) muss der Benutzer im Rahmen der Anmeldung einen CAPTCHA-Dialog beantworten.</li><li>Nach 10 weiteren erfolglosen Anmeldeversuchen (falsches Kennwort) und richtiger Beantwortung des CAPTCHA-Dialogs wird der Benutzer eine Zeit lang gesperrt. Eine weitere Eingabe falscher Kennwörter führt zu einer exponentiellen Verlängerung des Sperrzeitraums.</li></ul> |


## Nächste Schritte

* [Verwalten von Kennwörtern von einem beliebigen Ort aus](active-directory-passwords.md)
* [Funktionsweise der Kennwortverwaltung](active-directory-passwords-how-it-works.md)
* [Erste Schritte mit der Kennwortverwaltung](active-directory-passwords-getting-started.md)
* [Anpassen der Kennwortverwaltung](active-directory-passwords-customize.md)
* [Best Practices für die Kennwortverwaltung](active-directory-passwords-best-practices.md)
* [Operative Einblicke durch Berichte zur Kennwortverwaltung](active-directory-passwords-get-insights.md)
* [Häufig gestellte Fragen zur Kennwortverwaltung](active-directory-passwords-faq.md)
* [Problembehandlung für die Kennwortverwaltung](active-directory-passwords-troubleshoot.md)
* [Weitere Informationen](active-directory-passwords-learn-more.md)

<!---HONumber=Nov15_HO2-->