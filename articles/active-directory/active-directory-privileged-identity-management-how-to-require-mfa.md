<properties
   pageTitle="Erfordern von Multi-Factor Authentication | Microsoft Azure"
   description="Erfahren Sie, wie Sie mit der Erweiterung Azure Active Directory Privileged Identity Management Multi-Factor Authentication (MFA) als erforderlich festlegen."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management: Erfordern von MFA

Es wird empfohlen, von allen Administratoren Multi-Factor Authentication zu fordern.

## Erfordern von MFA in Azure AD Privileged Identity Management

Wenn Sie sich als PIM-Administrator anmelden, erhalten Sie Warnungen mit der Empfehlung, für die privilegierten Konten Multi-Factor Authentication (MFA) als erforderlich festzulegen. Klicken Sie im PIM-Dashboard auf die Sicherheitswarnung. Anschließend wird ein neues Blatt mit einer Liste der Administratorkonten geöffnet, für die MFA erforderlich sein sollte. Sie können MFA als erforderlich festlegen, indem Sie mehrere Rollen auswählen und dann auf die Schaltfläche **Korrigieren** klicken. Alternativ können Sie auf die Auslassungspunkte neben einzelnen Rollen und dann auf die Schaltfläche **Korrigieren** klicken.

Darüber hinaus können Sie die MFA-Anforderung für eine spezifische Rolle ändern. Klicken Sie hierzu im Abschnitt „Rollen“ des Dashboards auf die entsprechende Rolle, und aktivieren Sie dann MFA für diese Rolle, indem Sie im Blatt „Rolle“ auf **Einstellungen** klicken und dann unter „Multi-Factor Authentication“ die Option **Aktivieren** auswählen.

## Überprüfen der MFA durch Azure AD PIM

> [AZURE.IMPORTANT] Da die Registrierung von Microsoft-Konten (z. B. @outlook.com, @live.com oder @hotmail.com) für Azure MFA derzeit nicht unterstützt wird, werden diese Konten nicht als temporäre Administratoren für hoch privilegierte Rollen zugelassen. Wenn Benutzer weiterhin Workloads mit einem Microsoft-Konto verwalten müssen, wandeln Sie sie vorübergehend in permanente Administratoren um.

Wenn ein Benutzer eine Rolle aktiviert, stehen zwei Optionen zum Überprüfen von MFA zur Verfügung.

Die einfachste Methode besteht darin, sich bei Benutzern, die eine privilegierte Rolle aktivieren, auf Azure MFA zu verlassen. Überprüfen Sie dazu ggf. zunächst, ob diese Benutzer lizenziert sind und sich für Azure MFA registriert haben. Weitere Informationen hierzu finden Sie unter [Erste Schritte mit Azure Multi-Factor Authentication in der Cloud](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md#assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users). Beachten Sie, dass es zwar nicht erforderlich ist, jedoch empfohlen wird, Azure AD so zu konfigurieren, dass MFA für diese Benutzer bei ihrer Anmeldung erzwungen wird. Der Grund dafür ist, dass die MFA-Überprüfungen von Azure AD PIM selbst durchgeführt werden.

Wenn sich Benutzer lokal authentifizieren, können Sie alternativ festlegen, dass der Identitätsanbieter für MFA verantwortlich ist. Wenn Sie beispielsweise AD-Verbunddienste so konfiguriert haben, dass die smartcardbasierte Authentifizierung vor dem Zugriff auf Azure AD erforderlich ist, lesen Sie die Anweisungen zum Konfigurieren von AD FS für das Senden von Ansprüchen an Azure AD unter [Sichern von Cloudressourcen mit Azure Multi-Factor Authentication und AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md). Wenn ein Benutzer versucht, eine Rolle zu aktivieren, akzeptiert Azure AD PIM, dass MFA für den Benutzer bereits überprüft wurde, sobald die Anwendung die entsprechenden Ansprüche empfängt.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->