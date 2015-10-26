<properties
	pageTitle="Zuweisen von Administratorrollen in Azure AD"
	description="Erläutert, welche Administratorrollen in Azure AD verfügbar sind und wie sie zugewiesen werden."
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
	ms.date="09/21/2015"
	ms.author="curtand"/>

# Zuweisen von Administratorrollen in Azure AD

Abhängig von der Größe Ihres Unternehmens möchten Sie möglicherweise mehrere Administratoren ernennen, die verschiedene Funktionen ausüben. Diese Administratoren haben Zugriff auf verschiedene Funktionen im Azure-Portal und können abhängig von ihrer Rolle unter anderem Benutzer erstellen oder bearbeiten, anderen administrative Rollen zuweisen, Benutzerkennwörter zurücksetzen, Benutzerlizenzen verwalten und Domänen verwalten.

Das Verständnis dafür ist wichtig, dass ein Benutzer mit einer Administratorrolle die gleichen Berechtigungen für alle Cloud-Dienste besitzt, die Ihre Organisation abonniert hat, unabhängig davon, ob Sie die Rolle im Office 365-Portal, im Azure-Portal oder mithilfe des Azure AD-Moduls für Windows PowerShell zuweisen.

Die folgenden Administratorrollen sind verfügbar:

- **Rechnungsadministrator**: erledigt Käufe, verwaltet Abonnements, verwaltet Support-Tickets und überwacht die Integrität des Dienstes.
- **Globaler Administrator**: hat Zugriff auf alle administrativen Funktionen. Die Person, die die Anmeldung für das Azure-Konto vornimmt, wird ein globaler Administrator. Nur globale Administratoren können weitere Administratorrollen zuweisen. In Ihrem Unternehmen können mehrere globale Administratoren vorhanden sein.
- **Kennwortadministrator**: setzt Kennwörter zurück, verwaltet Dienstanforderungen und überwacht die Integrität des Diensts. Kennwortadministratoren können Kennwörter nur für Benutzer und andere Kennwortadministratoren zurücksetzen.
- **Dienstadministrator**: verwaltet Dienstanforderungen und überwacht die Integrität des Dienstes.
    
	> [AZURE.NOTE]
    	> Um einem Benutzer die Dienstadministratorrolle zuzuweisen, muss der globale Administrator zunächst dem Benutzer Administratorberechtigungen im Dienst (z. B. Exchange Online) zuweisen, und anschließend die Dienstadministratorrolle im Azure-Verwaltungsportal.
- **Benutzeradministrator**: setzte Kennwörter zurück, überwacht die Dienstintegrität und verwaltet Benutzerkonten, Benutzergruppen und Dienstanforderungen. Für die Berechtigungen eines Benutzerverwaltungsadministrators gelten einige Einschränkungen. Sie können z. B. keinen globalen Administrator löschen oder andere Administratoren erstellen. Sie können außerdem keine Kennwörter für Abrechnungs-, globale und Dienstadministratoren zurücksetzen.

## Administratorberechtigungen

### Abrechnungsadministrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens- und Benutzerinformationen</p><p>Verwalten von Office-Support-Tickets</p><p>Ausführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p> | <p>Zurücksetzen von Benutzerkennwörtern</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen, Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere</p><p>Verwenden von Verzeichnissynchronisierung</p>

### Globaler Administrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens-und Benutzerinformationen</p><p>Verwalten von Office-Support-Tickets</p><p>Ausführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p> <p>Zurücksetzen von Benutzerkennwörtern</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen, Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere</p><p>Verwenden von Verzeichnissynchronisierung</p> | –

### Kennwortadministrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens-und Benutzerinformationen</p><p>Verwalten von Office-Support-Tickets</p><p>Zurücksetzen von Kennwörtern</p> | <p>Ausführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen, Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere</p><p>Verwenden der Verzeichnissynchronisierung</p>

### Dienstadministrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens-und Benutzerinformationen</p><p>Verwalten von Office-Support-Tickets</p> | <p>Zurücksetzen von Benutzerkennwörtern</p><p>Ausführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen, Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere</p><p>Verwenden der Verzeichnissynchronisierung</p>

### Benutzeradministrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens-und Benutzerinformationen</p><p>Verwalten von Office-Support-Tickets</p><p>Zurücksetzen von Benutzerkennwörtern (mit Einschränkungen). Er kann keine Kennwörter für Abrechnungs-, globale und Dienstadministratoren zurücksetzen.</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen, Verwalten von Benutzerlizenzen (mit Einschränkungen). Er oder Sie kann keinen globalen Administrator löschen oder andere Administratoren erstellen.</p> | <p>Ausführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere</p><p>Verwenden der Verzeichnissynchronisierung</p>

## Details zur globalen Administratorrolle

Der globale Administrator hat Zugriff auf alle administrativen Funktionen. Standardmäßig wird der Person, die sich für ein Azure-Abonnement registriert, die globale Administratorrolle für das Verzeichnis zugewiesen. Nur globale Administratoren können weitere Administratorrollen zuweisen.

## Zuweisen oder Entfernen von Administratorrollen


1. Klicken Sie im Verwaltungsportal auf **Active Directory**, und klicken Sie dann auf den Namen des Verzeichnisses Ihrer Organisation.
2. Auf der Seite **Benutzer** klicken Sie auf den Anzeigenamen des Benutzers, den Sie bearbeiten möchten.
3. Wählen Sie das Dropdown-Menü **Organisationsrolle**, und wählen Sie dann die Administratorrolle, die diesem Benutzer zugewiesen werden soll, oder wählen Sie **Benutzer**, wenn Sie eine vorhandene Administratorrolle entfernen möchten.
4. Im Feld **Alternative E-Mail-Adresse** geben Sie eine E-Mail-Adresse ein. Diese E-Mail-Adresse wird für wichtige Benachrichtigungen, einschließlich des automatischen Zurücksetzens des Kennworts verwendet, so dass der Benutzer Zugriff auf das E-Mail-Konto benötigt, und zwar unabhängig davon, ob er auf Azure zugreifen kann oder nicht.
5. Wählen Sie **Zulassen** oder **Blockieren**, um anzugeben, ob der Benutzer sich anmelden kann und Zugriff auf die Dienste bekommt.
6. Geben Sie einen Speicherort aus der Dropdown-Liste **Nutzungsspeicherort** an.
7. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

## Nächste Schritte

- [Verwalten von Benutzern](../active-directory-manage-users.md)
- [Verwalten von Kennwörtern](active-directory-manage-passwords.md)
- [Gruppen verwalten](active-directory-manage-groups.md)

<!---HONumber=Oct15_HO3-->