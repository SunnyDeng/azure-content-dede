<properties
	pageTitle="Löschen von benutzerdefinierten Domänen in Azure Active Directory | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine benutzerdefinierte Domäne in Azure Active Directory löschen."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>


# Löschen benutzerdefinierter Domänennamen

Sie können einen benutzerdefinierten Domänennamen, den Sie nicht mehr für Ihr Azure AD-Verzeichnis verwenden möchten, löschen. Beispiele für solche Situationen sind z. B. die Änderung Ihres Unternehmensnamens oder die Verwendung eines anderen Azure AD-Verzeichnisses. Sie können auch eine nicht überprüfte Domäne löschen, z. B. wenn Sie nach dem Hinzufügen feststellen, dass Sie den Namen falsch eingegeben haben, oder wenn Sie den Wert falsch festgelegt haben, um die Domäne einem Verbund hinzuzufügen.

## Voraussetzungen

Bevor Sie einen Domänennamen entfernen, wird empfohlen, dass Sie die folgende Informationen lesen:

- Der ursprüngliche Domänenname contoso.onmicrosoft.com, der bei der Registrierung für Ihr Verzeichnis bereitgestellt wurde, kann nicht entfernt werden.
- Jede Domäne der obersten Ebene, denen Unterdomänen zugeordnet sind, werden nicht entfernt, bis die Unterdomänen entfernt wurden. Beispielsweise können Sie adatum.com nicht entfernen, wenn Sie corp.adatum.com oder eine andere Unterdomäne vorliegen haben, die den Namen der Domäne der obersten Ebene verwendet. Weitere Informationen finden Sie im Supportartikel [Fehler „Domäne verfügt über zugeordnete Unterdomänen“ oder „Eine Domäne, die über Unterdomänen verfügt, können Sie nicht entfernen“ beim Entfernen einer Domäne aus Office 365](https://support.microsoft.com/kb/2787792/).
- Haben Sie die Verzeichnissynchronisierung aktiviert? Falls ja, wurde eine Domäne automatisch zu Ihrem Konto hinzugefügt. Sie sieht in etwa wie folgt aus: contoso.mail.onmicrosoft.com. Dieser Domänenname kann nicht entfernt werden.
- Bevor Sie einen Domänennamen entfernen können, müssen Sie zuerst den Domänennamen aus allen Benutzer- oder E-Mail-Konten entfernen, die mit der Domäne verbunden sind. Sie können alle Konten entfernen, oder Sie können mehrere Benutzerkonten gleichzeitig bearbeiten, um deren Domänenameninformationen und E-Mail-Adressen zu ändern. Weitere Informationen finden Sie unter [Erstellen oder Bearbeiten von Benutzern in Azure AD](active-directory-create-users.md). Denken Sie daran, Folgendes zu entfernen:

	-   Alle Benutzer mit dem Domänennamen im Benutzernamen oder in der E-Mail-Adresse

	-   Alle E-Mail-aktivierten Gruppen mit der Domäne in der E-Mail-Adresse

	-   Alle Anwendungen mit der Domäne als Teil der Antwort-URL

- Wenn Sie eine SharePoint Online-Website auf einen Domänennamen hosten, der für eine SharePoint Online-Websitesammlung verwendet wird, müssen Sie die Websitesammlung löschen, bevor Sie den Domänennamen entfernen können.

## So löschen Sie eine Domäne

1.  Melden Sie sich mit einem Konto mit globalen Administratorberechtigungen für das Verzeichnis beim klassischen Azure-Portal an.

2.  Öffnen Sie Ihr Verzeichnis, und wählen Sie **Domänen** aus.

3.  Wählen Sie die Domäne aus, und klicken Sie auf „Löschen“.

## Nächste Schritte

- [Verwenden von benutzerdefinierten Domänennamen zum Vereinfachen des Anmeldevorgangs für Benutzer](active-directory-add-domain.md)
- [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md)
- [Hinzufügen und Überprüfen eines benutzerdefinierten Domänennamens in Azure Active Directory](active-directory-add-domain-add-verify-general.md)
- [Zuweisen von Benutzern zu einer benutzerdefinierten Domäne](active-directory-add-domain-add-users.md)
- [Ändern der DNS-Registrierungsstelle für Ihren benutzerdefinierten Domänennamen](active-directory-add-domain-change-registrar.md)

<!---HONumber=AcomDC_0211_2016-->