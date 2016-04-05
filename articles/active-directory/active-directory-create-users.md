<properties
	pageTitle="Hinzufügen von Benutzern oder Ändern von Benutzerinformationen in Azure Active Directory | Microsoft Azure"
	description="Es wird beschrieben, wie Sie in Azure Active Directory Benutzer hinzufügen oder Benutzerinformationen ändern, z.B. externe Benutzer und Gastbenutzer."
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
	ms.topic="get-started-article"
	ms.date="03/24/2016"
	ms.author="curtand;viviali"/>

# Hinzufügen oder Ändern von Benutzern in Azure Active Directory

Sie müssen für jeden Benutzer, der auf einen Microsoft-Clouddienst zugreifen soll, dem Verzeichnis Ihres Mandanten ein Konto hinzufügen. Hinzugefügte Benutzer verfügen nicht standardmäßig über Administratorberechtigungen, aber Sie können ihnen jederzeit Rollen zuweisen.

## Hinzufügen eines Benutzers

1. Melden Sie sich am [klassischen Azure-Portal](https://manage.windowsazure.com) mit einem Konto an, bei dem es sich um einen globalen Administrator für das Verzeichnis handelt.
2. Wählen Sie **Active Directory** und dann den Namen des Verzeichnisses Ihrer Organisation.
3. Wählen Sie die Registerkarte **Benutzer** und dann im Befehlsbereich die Option **Benutzer hinzufügen**.
4. Wählen Sie auf der Seite **Informationen über diesen Benutzer** unter **Art des Benutzers** eine der folgenden Optionen:

	- **Neuer Benutzer in Ihrem Unternehmen:** Dient zum Hinzufügen eines neuen Benutzerkontos in Ihrem Verzeichnis.
	- **Benutzer mit einem vorhandenen Microsoft-Konto:** Dient zum Hinzufügen eines vorhandenen Microsoft-Kundenkontos (z.B. eines Outlook-Kontos) zu Ihrem Verzeichnis.
	- **Benutzer in einem anderen Microsoft Azure AD-Verzeichnis:** Dient zum Hinzufügen eines Benutzerkontos zu Ihrem Verzeichnis, das aus einem anderen Azure AD-Verzeichnis erstellt wurde. (Hinweis: Sie müssen Mitglied des anderen Verzeichnisses sein, um darin einen Benutzer auswählen zu können.)
	- **Benutzer in Partnerunternehmen:** Dient zum Einladen und Autorisieren eines Partnerunternehmens für Ihr Verzeichnis (siehe [Azure Active Directory B2B-Zusammenarbeit](active-directory-b2b-what-is-azure-ad-b2b.md)).


5. Geben Sie je nach ausgewähltem Benutzertyp einen Benutzernamen oder eine E-Mail-Adresse ein, oder laden Sie eine CSV-Datei mit den E-Mail-Adressen der Benutzer hoch, die sich anmelden können.
6. Geben Sie auf der Seite **Profil** des Benutzers den Vornamen und Nachnamen des Benutzers, einen benutzerfreundlichen Namen und über die Liste **Rollen** eine Benutzerrolle an. Weitere Informationen zu Benutzer- und Administratorrollen finden Sie unter [Zuweisen von Administratorrollen in Azure AD](active-directory-assign-admin-roles.md). Geben Sie an, ob **Mehrstufige Authentifizierung aktivieren** verwendet werden soll.
7. Wählen Sie auf der Seite **Temporäres Kennwort abrufen** die Option **Erstellen**.

> [AZURE.IMPORTANT] Achten Sie auf die folgenden Probleme, die beim Hinzufügen eines Benutzerkontos auftreten können, wenn Ihre Organisation mehr als eine Domäne verwendet:
>
> - Sie können Benutzerkonten mit dem gleichen Benutzerprinzipalnamen (User Principal Name, UPN) auch domänenübergreifend hinzufügen, wenn Sie beispielsweise zuerst geoffgrisso@contoso.onmicrosoft.com und dann geoffgrisso@contoso.com hinzufügen.
> - Sie dürfen geoffgrisso@contoso.com nicht hinzufügen, bevor Sie geoffgrisso@contoso.onmicrosoft.com hinzugefügt haben. Diese Reihenfolge ist äußerst wichtig, und es kann umständlich sein, diesen Vorgang rückgängig zu machen.

## Ändern von Benutzerinformationen

Sie können alle Benutzerattribute ändern, mit Ausnahme der Objekt-ID des Benutzers.

1. Öffnen Sie das Verzeichnis Ihrer Organisation.
2. Wählen Sie die Registerkarte **Benutzer**, und wählen Sie dann den Anzeigenamen des Benutzers aus, den Sie ändern möchten.
3. Geben Sie die Änderungen ein, und klicken Sie auf **Speichern**.

Wenn der Benutzer, den Sie ändern möchten, mit Ihrem lokalen Active Directory-Dienst synchronisiert wird, wird eine Fehlermeldung angezeigt. Sie können die Benutzerinformationen dann nicht mit diesem Verfahren ändern. Verwenden Sie zum Ändern des Benutzers Ihre lokalen Active Directory-Verwaltungstools.

## Zurücksetzen des Benutzerkennworts

1. Öffnen Sie das Verzeichnis Ihrer Organisation.
2. Wählen Sie die Registerkarte **Benutzer**, und wählen Sie dann den Anzeigenamen des Benutzers aus, den Sie ändern möchten.
3. Wählen Sie im Befehlsbereich die Option **Kennwort zurücksetzen**.
4. Klicken Sie im Dialogfeld zum Zurücksetzen des Kennworts auf **Zurücksetzen**.
5. Aktivieren Sie das Kontrollkästchen, um das Zurücksetzen des Kennworts abzuschließen.

## Hinzufügen von externen Benutzern

In Azure AD können Sie auch einem Azure AD-Verzeichnis mit einem Microsoft-Konto Benutzer aus einem anderen Azure AD-Verzeichnis hinzufügen, dessen Mitglied Sie sind, oder Benutzer von Partnerunternehmen, indem Sie eine CSV-Datei hochladen. Zum Hinzufügen eines externen Benutzers fügen Sie diesen im Portal hinzu und wählen unter **Art des Benutzers** eine der Optionen **Benutzer in einem anderen Microsoft Azure AD-Verzeichnis** oder **Benutzer in Partnerunternehmen** aus.

Benutzer dieser Typen stammen aus einem anderen Verzeichnis und werden als **externe Benutzer** hinzugefügt. Externe Benutzer können mit Benutzern zusammenarbeiten, die in einem Verzeichnis bereits vorhanden sind, indem sie ihr zentrales Konto verwenden. Es müssen also keine neuen Konten und Anmeldeinformationen hinzugefügt werden. Externe Benutzer werden über ihr Basisverzeichnis authentifiziert, wenn sie sich anmelden. Diese Authentifizierung funktioniert auch für alle anderen Verzeichnisse, denen sie hinzugefügt wurden.

## Verwaltung externer Benutzer und Einschränkungen

Wenn Sie einen Benutzer aus einem anderen Verzeichnis Ihrem Verzeichnis hinzufügen, wird er in Ihrem Verzeichnis zu einem externen Benutzer. Zuerst werden der Anzeigename und der Benutzername aus dem so genannten „Basisverzeichnis“ des Benutzers kopiert und für den externen Benutzer in Ihrem Verzeichnis verwendet. Ab diesem Zeitpunkt sind diese und andere Eigenschaften des externen Benutzerkontos vollständig unabhängig voneinander: Wenn im Basisverzeichnis eine Änderung für diesen Benutzer vorgenommen wird, also z. B. eine Änderung des Benutzernamens, das Hinzufügen seiner Position usw., werden die Änderungen nicht für das Konto des externen Benutzers in Ihrem Verzeichnis übernommen.

Die einzige Verbindung zwischen den beiden Konten ist, dass der Benutzer immer anhand seines Basisverzeichnisses oder über sein Microsoft-Konto authentifiziert wird. Daher ist keine Option zum Zurücksetzen des Kennworts oder zum Aktivieren der Multi-Factor Authentication für einen externen Benutzer vorhanden. Derzeit wird ausschließlich die Authentifizierungsrichtlinie des Basisverzeichnisses oder des Microsoft-Kontos verwendet, die beim Anmelden des Benutzers ausgewertet wird.

> [AZURE.NOTE]
Sie können den Benutzer im externen Verzeichnis aber trotzdem deaktivieren. Dadurch wird der Zugriff auf Ihr Verzeichnis blockiert.

Wenn ein Benutzer in seinem Basisverzeichnis gelöscht wird oder sein Microsoft-Konto kündigt, ist der externe Benutzer weiterhin in Ihrem Verzeichnis vorhanden. Er kann aber nicht mehr auf Ressourcen in Ihrem Verzeichnis zugreifen, da die Authentifizierung über das Basisverzeichnis oder das Microsoft-Konto nicht mehr möglich ist.

Hier sind die Dienste aufgeführt, die den Zugriff durch externe Azure AD-Benutzer derzeit unterstützen:

- **Klassisches Azure-Portal:** Ermöglicht einem externen Benutzer, der Administrator mehrerer Verzeichnisse ist, das Verwalten dieser Verzeichnisse.
- **SharePoint Online:** Ermöglicht einem externen Benutzer den Zugriff auf autorisierte Ressourcen von SharePoint Online, wenn die externe Freigabe aktiviert ist.
- **Dynamics CRM:** Ermöglicht einem externen Benutzer den Zugriff auf autorisierte Ressourcen in Dynamics CRM, wenn der Benutzer per PowerShell lizenziert ist.

Die bekannten Einschränkungen für externe Azure AD-Benutzer lauten:

- Externe Benutzer, bei denen es sich um Administratoren handelt, können Benutzer von Partnerunternehmen nicht Verzeichnissen (B2B-Zusammenarbeit) außerhalb ihres Basisverzeichnisses hinzufügen.
- Externe Benutzer können mehrinstanzenfähigen Anwendungen in Verzeichnissen außerhalb ihres Basisverzeichnisses nicht ihre Zustimmung geben.
- Visual Studio Online unterstützt den Zugriff durch externe Benutzer derzeit nicht.*
- PowerBI unterstützt den Zugriff durch externe Benutzer derzeit nicht.
- Das Office-Portal unterstützt die Lizenzierung externer Benutzer nicht.

* Visual Studio Online lässt den Zugriff durch externe Benutzer über Microsoft-Konten zu, jedoch nicht durch externe Benutzer, die sich mit Geschäfts-, Schul- oder Unikonten authentifizieren.

## Verwaltung von Gastbenutzern und Einschränkungen

Gastkonten sind Benutzer aus anderen Verzeichnissen, die in Ihr Verzeichnis eingeladen wurden und auf eine bestimmte Ressource zugreifen können, z.B. ein SharePoint Online-Dokument, eine Anwendung oder eine Azure-Ressource. Für ein Gastkonto in Ihrem Verzeichnis ist das zugrunde liegende UserType-Attribut auf „Gast“ festgelegt. Reguläre Benutzer (also Mitglieder Ihres Verzeichnisses) verfügen über das UserType-Attribut „Member“.

Gäste verfügen im Verzeichnis über eingeschränkte Berechtigungen. Diese Berechtigungen bewirken, dass Gäste nicht auf alle Informationen zu anderen Benutzern im Verzeichnis zugreifen können. Sie können mit den Benutzern und Gruppen, die den bearbeiteten Ressourcen zugeordnet sind, aber trotzdem interagieren. Gastbenutzer haben folgende Möglichkeiten:

- Anzeigen anderer Benutzer und Gruppen eines Azure-Abonnements, dem sie zugewiesen sind
- Anzeigen der Mitglieder von Gruppen, denen sie angehören
- Suchen nach anderen Benutzern im Verzeichnis, sofern die vollständige E-Mail-Adresse des Benutzers bekannt ist
- Anzeigen einer begrenzten Gruppe von Attributen der gesuchten Benutzer (Anzeigename, E-Mail-Adresse, Benutzerprinzipalname (UPN) und Miniaturbild)
- Abrufen einer Liste mit den überprüften Domänen im Mandantenverzeichnis
- Erteilen der Zustimmung für Anwendungen und Gewähren des gleichen Zugriffs, der für Mitglieder in Ihrem Verzeichnis gilt

## Festlegen von Richtlinien für den Benutzerzugriff

Die Registerkarte **Konfigurieren** eines Verzeichnisses enthält Optionen zur Zugriffssteuerung für externe Benutzer. Diese Optionen können nur im klassischen Azure-Portal von einem globalen Verzeichnisadministrator geändert werden (es gibt keine Windows PowerShell- oder API-Methode).

Wählen Sie zum Öffnen im klassischen Azure-Portal die Registerkarte **Konfigurieren** und dann **Active Directory** und den Namen des Verzeichnisses.

![Registerkarte „Konfigurieren“ in Azure Active Directory][1]

Anschließend können Sie die Optionen zur Zugriffssteuerung für externe Benutzer ändern.

![][2]


## Nächste Schritte

- [Verwalten von Azure AD](active-directory-administer.md)
- [Verwalten von Kennwörtern in Azure AD](active-directory-manage-passwords.md)
- [Verwalten von Gruppen in Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=AcomDC_0330_2016-->