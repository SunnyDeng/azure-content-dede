<properties
	pageTitle="Erstellen oder Bearbeiten von Benutzern in Azure Active Directory | Microsoft Azure"
	description="In diesem Thema wird beschrieben, wie Sie Benutzerkonten unter Azure Active Directory erstellen oder bearbeiten."
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
	ms.author="curtand;viviali"/>

# Erstellen oder Bearbeiten von Benutzern in Azure AD

Sie müssen für jeden Benutzer, der auf einen Microsoft-Clouddienst zugreifen soll, ein eigenes Konto erstellen. Sie können Benutzerkonten auch ändern oder löschen, wenn sie nicht mehr benötigt werden. Benutzer haben standardmäßig keine Administratorberechtigungen, aber Sie können ihnen diese erteilen.

## Erstellen eines Benutzers

1. Klicken Sie auf **Active Directory**, und wählen Sie dann den Namen des Verzeichnisses Ihrer Organisation.
2. Klicken Sie auf der Seite **Benutzer** auf **Benutzer hinzufügen**.
3. Wählen Sie auf der Seite **Informationen über diesen Benutzer** unter **Art des Benutzers** eine der folgenden Optionen:
	
	- **Neuer Benutzer in Ihrem Unternehmen:** Dient zum Erstellen eines neuen Benutzerkontos in Ihrem Verzeichnis.
	- **Benutzer mit einem vorhandenen Microsoft-Konto:** Dient zum Hinzufügen eines vorhandenen Microsoft-Kundenkontos (z. B. eines Outlook-Kontos) zu Ihrem Verzeichnis.
	- **Benutzer in einem anderen Microsoft Azure AD-Verzeichnis:** Dient zum Hinzufügen eines Benutzerkontos zu Ihrem Verzeichnis, das aus einem anderen Azure AD-Verzeichnis erstellt wurde. (Hinweis: Sie müssen Mitglied des anderen Verzeichnisses sein, um darin einen Benutzer auswählen zu können.)
	- **Benutzer in Partnerunternehmen:** Dient zum Einladen und Autorisieren eines Partnerunternehmens für Ihr Verzeichnis (siehe [Azure Active Directory B2B](active-directory-b2b-what-is-azure-ad-b2b.md)).
	

4. Geben Sie je nach ausgewählter Option entweder einen Benutzernamen oder eine E-Mail-Adresse ein, oder laden Sie eine CSV-Datei für Partnerbenutzer hoch.
5. Geben Sie auf der Seite **Profil** des Benutzers den Vornamen und Nachnamen des Benutzers, einen benutzerfreundlichen Namen und über das Dropdownmenü „Rollen“ eine Benutzerrolle an. Weitere Informationen zu Benutzer- und Administratorrollen finden Sie unter [Zuweisen von Administratorrollen in Azure AD](active-directory-assign-admin-roles.md). Geben Sie an, ob **Mehrstufige Authentifizierung aktivieren** verwendet werden soll.
6. Klicken Sie auf der Seite **Temporäres Kennwort abrufen** auf **Erstellen**.

Achten Sie auf die folgenden Probleme, die beim Erstellen eines Benutzerkontos auftreten können, wenn Ihre Organisation mehr als eine Domäne verwendet:

- Sie können Benutzerkonten mit dem gleichen Benutzerprinzipalnamen (User Principal Name, UPN) auch domänenübergreifend erstellen, wenn Sie beispielsweise zuerst geoffgrisso@contoso.onmicrosoft.com und dann geoffgrisso@contoso.com erstellen.
- Das Erstellen von geoffgrisso@contoso.com gefolgt von geoffgrisso@contoso.onmicrosoft.com ist nicht möglich.

## Bearbeiten eines Benutzers

So bearbeiten Sie einen Benutzer im klassischen Azure-Portal:

1. Klicken Sie auf **Active Directory** und dann auf den Namen des Verzeichnisses Ihrer Organisation.
2. Auf der Seite **Benutzer** klicken Sie auf den Anzeigenamen des Benutzers, den Sie bearbeiten möchten.
3. Geben Sie die Änderungen ein, und klicken Sie auf **Speichern**.

Wenn der Benutzer, den Sie bearbeiten möchten, mit Ihrem lokalen Active Directory-Dienst synchronisiert wird, wird eine Fehlermeldung angezeigt. Sie können den Benutzer mit diesem Verfahren dann nicht bearbeiten. Verwenden Sie zum Bearbeiten des Benutzers Ihre lokalen Active Directory-Verwaltungstools.

## Zurücksetzen des Kennworts für einen Benutzer

1. Klicken Sie auf **Active Directory** und dann auf den Namen des Verzeichnisses Ihrer Organisation.
2. Auf der Seite **Benutzer** klicken Sie auf den Anzeigenamen des Benutzers, den Sie bearbeiten möchten.
3. Klicken Sie unten im Portal auf **Kennwort zurücksetzen**.
4. Klicken Sie im Dialogfeld zum Zurücksetzen des Kennworts auf **Zurücksetzen**.
5. Klicken Sie auf das Häkchen, um zu bestätigen, dass das Kennwort zurückgesetzt wurde.

## Erstellen von externen Benutzern

In Azure AD können Sie Benutzer auch einem Azure AD-Verzeichnis mit einem Microsoft-Konto aus einem anderen Azure AD-Verzeichnis hinzufügen, dessen Mitglied Sie sind, oder Benutzer von Partnerunternehmen, indem Sie eine CSV-Datei hochladen. Beim Erstellen eines externen Benutzers erstellen Sie diesen im Portal und wählen unter **Art des Benutzers** die Option **Benutzer in einem anderen Microsoft Azure AD-Verzeichnis** oder **Benutzer in Partnerunternehmen**.

Die mit diesen Verfahren erstellten Benutzer stammen aus einem anderen Verzeichnis und werden als **externe Benutzer** erstellt. Externe Benutzer können mit Benutzern zusammenarbeiten, die in einem Verzeichnis bereits vorhanden sind, indem sie ihr zentrales Konto verwenden. Es müssen also keine neuen Konten und Anmeldeinformationen erstellt werden. Externe Benutzer werden über ihr Basisverzeichnis authentifiziert, wenn sie sich anmelden. Diese Authentifizierung funktioniert auch für alle anderen Verzeichnisse, an denen sie beteiligt sind.

## Verwaltung externer Benutzer und Einschränkungen

Wenn Sie einen Benutzer aus einem anderen Verzeichnis Ihrem Verzeichnis hinzufügen, wird er in Ihrem Verzeichnis zu einem externen Benutzer. Zuerst werden der Anzeigename und der Benutzername aus dem so genannten „Basisverzeichnis“ des Benutzers kopiert und für den externen Benutzer in Ihrem Verzeichnis verwendet. Ab diesem Zeitpunkt sind diese und andere Eigenschaften des externen Benutzerobjekts vollständig unabhängig voneinander: Wenn im Basisverzeichnis eine Änderung für diesen Benutzer vorgenommen wird, also z. B. eine Änderung des Benutzernamens, das Hinzufügen seiner Position usw., werden die Änderungen nicht für das Objekt des externen Benutzers in Ihrem Verzeichnis übernommen.

Die einzige Verbindung zwischen den beiden Objekten ist, dass der Benutzer immer anhand seines Basisverzeichnisses oder über sein Microsoft-Konto authentifiziert wird. Daher ist keine Option zum Zurücksetzen des Kennworts oder zum Aktivieren der Multi-Factor Authentication für einen externen Benutzer vorhanden. Derzeit wird ausschließlich die Authentifizierungsrichtlinie des Basisverzeichnisses oder des Microsoft-Kontos verwendet, die beim Anmelden des Benutzers ausgewertet wird.

> [AZURE.NOTE]
Sie können den Benutzer im externen Verzeichnis aber trotzdem deaktivieren. Dadurch wird der Zugriff auf Ihr Verzeichnis blockiert.

Wenn ein Benutzer in seinem Basisverzeichnis gelöscht wird oder sein Microsoft-Konto kündigt, ist der externe Benutzer weiterhin in Ihrem Verzeichnis vorhanden. Er kann aber nicht mehr auf Ressourcen in Ihrem Verzeichnis zugreifen, da die Authentifizierung über das Basisverzeichnis oder das Microsoft-Konto nicht mehr möglich ist.

Hier sind die Dienste aufgeführt, die den Zugriff durch externe Azure AD-Benutzer derzeit unterstützen:

- Klassisches Azure-Portal: Ermöglicht einem externen Benutzer, der Administrator mehrerer Verzeichnisse ist, das Verwalten dieser Verzeichnisse.
- SharePoint Online: Ermöglicht einem externen Benutzer den Zugriff auf autorisierte Ressourcen von SharePoint Online, wenn die externe Freigabe aktiviert ist.
- Dynamics CRM: Ermöglicht einem externen Benutzer den Zugriff auf autorisierte Ressourcen in Dynamics CRM, wenn der Benutzer per PowerShell lizenziert ist.

Die bekannten Einschränkungen für externe Azure AD-Benutzer lauten:

- Externe Benutzer, bei denen es sich um Administratoren handelt, können Benutzer von Partnerunternehmen nicht Verzeichnissen (B2B) außerhalb ihres Basisverzeichnisses hinzufügen.
- Externe Benutzer können mehrinstanzenfähigen Anwendungen in Verzeichnissen außerhalb ihres Basisverzeichnisses nicht ihre Zustimmung geben.
- Visual Studio Online unterstützt den Zugriff durch externe Benutzer derzeit nicht.
- PowerBI unterstützt den Zugriff durch externe Benutzer derzeit nicht. 
- Das Office-Portal unterstützt die Lizenzierung externer Benutzer nicht.

## Gäste

Ein **Gast** ist ein Benutzer in Ihrem Verzeichnis, für den das Attribut UserType auf „Guest“ (Gast) festgelegt ist. Für normale Benutzer ist UserType auf „Member“ (Mitglied) festgelegt, um anzugeben, dass sie Mitglied Ihres Verzeichnisses sind. Gäste sind Benutzer aus anderen Verzeichnissen, die in Ihr Verzeichnis eingeladen wurden und auf eine bestimmte Ressource zugreifen können, z. B. ein SharePoint-Dokument, eine Anwendung oder eine Azure-Ressource.

Gäste verfügen im Verzeichnis über eingeschränkte Berechtigungen. Diese Berechtigungen bewirken, dass Gäste nicht auf alle Informationen zu anderen Benutzern im Verzeichnis zugreifen können. Sie können mit den Benutzern und Gruppen, die den bearbeiteten Ressourcen zugeordnet sind, aber trotzdem interagieren. Gäste verfügen über die folgenden Funktionen:

- Anzeigen anderer Benutzer und Gruppen eines Azure-Abonnements, dem der Gast zugeordnet ist
- Anzeigen der Mitglieder von Gruppen, denen der Gast angehört
- Suchen nach anderen Benutzern im Verzeichnis, falls die vollständige E-Mail-Adresse des Benutzers bekannt ist
- Anzeigen einer begrenzten Gruppe von Attributen der gesuchten Benutzer (Anzeigename, E-Mail-Adresse, Benutzerprinzipalname (UPN) und Miniaturbild) 
- Abrufen einer Liste der überprüften Domänen des Mandanten
- Erteilen der Zustimmung für Anwendungen und Gewähren des gleichen Zugriffs, der für den Gast in Ihrem Verzeichnis gilt

## Konfigurieren von Richtlinien für den Benutzerzugriff

Die Registerkarte **Konfigurieren** eines Verzeichnisses enthält Optionen zur Zugriffssteuerung für externe Benutzer. Diese Optionen können nur über die Benutzeroberfläche (es gibt keine Windows PowerShell- oder API-Methode) im klassischen Azure-Portal von einem globalen Verzeichnisadministrator geändert werden. Klicken Sie zum Öffnen im klassischen Azure-Portal auf die Registerkarte **Konfigurieren** und dann auf **Active Directory** und den Namen des Verzeichnisses.

![][1]

Anschließend können Sie die Optionen zur Zugriffssteuerung für externe Benutzer ändern.

![][2]


## Nächste Schritte

- [Verwalten von Azure AD](active-directory-administer.md)
- [Verwalten von Kennwörtern in Azure AD](active-directory-manage-passwords.md)
- [Verwalten von Gruppen in Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=AcomDC_0204_2016-->