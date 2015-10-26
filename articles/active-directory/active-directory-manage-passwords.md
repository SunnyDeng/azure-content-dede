<properties
	pageTitle="Verwalten von Kennwörtern in Azure AD | Microsoft Azure"
	description="Verwalten von Kennwörtern in Azure AD"
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

# Verwalten von Kennwörtern in Azure AD

Wenn Sie Administrator sind, können Sie im klassischen Azure-Portal das Kennwort eines Benutzers in Azure zurücksetzen. Klicken Sie auf den Namen des Verzeichnisses und dann auf der Seite "Benutzer" auf den Namen des Benutzers und anschließend unten im Portal auf **Kennwort zurücksetzen**.

Im weiteren Verlauf dieses Themas werden die umfassenden Kennwortverwaltungsfunktionen behandelt , die Azure Active Directory unterstützt, einschließlich:

- Mit der **Self-Service-Kennwortänderung** können Endbenutzer oder Administratoren ihre abgelaufenen oder nicht abgelaufenen Kennwörter ändern, ohne einen Administrator oder das Helpdesk um Unterstützung zu bitten.
- Mit der **Self-Service-Kennwortzurücksetzung ** können Endbenutzer oder Administratoren ihre Kennwörter automatisch zurücksetzen, ohne einen Administrator oder das Helpdesk um Unterstützung zu bitten. Die Self-Service-Kennwortzurücksetzung erfordert Azure AD Premium oder Basic. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).
- Mit der vom **Administrator ausgelösten Kennwortzurücksetzung** kann ein Administrator über das Azure-Verwaltungsportal das Kennwort eines Endbenutzers oder eines anderen Administrators zurücksetzen.
- **Berichte zu Kennwortverwaltungsaktivitäten** bieten Administratoren Einblicke in die Aktivitäten, die sich in ihrer Organisation in Bezug auf die Kennwortzurücksetzung und -registrierung ereignen.
- Das **Rückschreiben von Kennwörtern** ermöglicht die Verwaltung lokaler Kennwörter in der Cloud, sodass alle oben genannten Szenarien von oder im Auftrag von Verbund- oder kennwortsynchronisierten Benutzern durchgeführt werden können. Für das Rückschreiben von Kennwörtern ist Azure AD Premium erforderlich. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Active Directory Premium](active-directory-get-started-premium.md).

> [AZURE.NOTE]Azure AD Premium steht Kunden in China über die internationale Instanz von Azure AD zur Verfügung. Azure AD Premium wird derzeit im von 21Vianet in China betriebenen Microsoft Azure-Dienst nicht unterstützt. Wenn Sie weitere Informationen benötigen, kontaktieren Sie uns im [Azure Active Directory-Forum](http://feedback.azure.com/forums/169401-azure-active-directory).

Über die folgenden Links gelangen Sie zur Dokumentation, die für Sie am interessantesten ist:

- [Übersicht: Kennwortverwaltung in Azure AD](active-directory-passwords-how-it-works.md)
- [Self-Service-Kennwortzurücksetzung in Azure AD: Aktivierung, Konfiguration und Test der Self-Service-Kennwortzurücksetzung ](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)
- [Self-Service-Kennwortzurücksetzung in Azure AD: Anpassen der Kennwortzurücksetzung zum Erfüllen Ihrer Anforderungen](active-directory-passwords-customize.md)
- [Self-Service-Kennwortzurücksetzung in Azure AD: Bewährte Methoden für die Bereitstellung und Verwaltung](active-directory-passwords-best-practices.md)
- [Kennwortverwaltungsberichte in Azure AD: Anzeigen von Kennwortverwaltungsaktivitäten in Ihrem Mandanten](active-directory-passwords-get-insights.md)
- [Rückschreiben von Kennwörtern: Konfigurieren von Azure AD für die Verwaltung lokaler Kennwörter](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
- [Behandeln von Problemen bei der Kennwortverwaltung von Azure AD](active-directory-passwords-troubleshoot.md)
- [Häufig gestellte Fragen zur Kennwortverwaltung von Azure AD](active-directory-passwords-faq.md)


## Nächste Schritte

- [Verwalten von Azure AD](active-directory-administer.md)
- [Erstellen oder Bearbeiten von Benutzern in Azure AD](active-directory-create-users.md)
- [Verwalten von Gruppen in Azure AD](active-directory-manage-groups.md)

<!---HONumber=Oct15_HO3-->