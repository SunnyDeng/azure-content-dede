<properties
	pageTitle="Azure Active Directory-Editionen| Microsoft Azure"
	description="In diesem Thema werden die Features und Unterschiede der kostenlosen und kostenpflichtigen Editions von Azure Active Directory beschrieben."
	services="active-directory"
	documentationCenter=""
	authors="MarkusVi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/04/2015"
	ms.author="markvi"/>

# Azure Active Directory-Editionen

Alle Microsoft Online-Unternehmensdienste nutzen für Anmelde- und andere Identitätsanforderungen Azure Active Directory. Wenn Sie einen der Microsoft Online-Unternehmensdienste (z. B. Office 365 oder Microsoft Azure) abonnieren, erhalten Sie Azure Active Directory (Azure AD) mit Zugriff auf alle unten beschriebenen Free-Features.

Azure Active Directory ist ein Dienst, der Ihren Mitarbeitern, Partnern und Kunden umfassende Funktionen zur Identitäts- und Zugriffsverwaltung in der Cloud bietet. Er kombiniert Verzeichnisdienste, eine erweiterte Identitätskontrolle, eine standardbasierte Plattform für Entwickler mit umfangreichen Funktionen und Zugriffsverwaltung für eigene oder tausende vorab integrierte Anwendungen. Mit der Free-Edition von Azure Active Directory können Sie Benutzer und Gruppen verwalten, eine Synchronisierung mit lokalen Verzeichnissen ausführen, sich mittels einmaligem Anmelden (SSO) bei Azure, Office 365 und tausenden gängigen SaaS-Anwendungen wie Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox und mehr anmelden. Weitere Informationen zu Azure Active Directory finden Sie unter [Was ist Azure AD?](active-directory-whatis.md).


Sie können Ihre Azure Active Directory-Anwendung erweitern, indem Sie mit Azure Active Directory Basic und Premium kostenpflichtige Funktionen hinzufügen. Die kostenpflichtigen Editionen von Azure Active Directory ergänzen Ihr kostenloses Verzeichnis. Sie bieten leistungsstarke Funktionen wie etwa Self-Service, optimierte Überwachung, Sicherheitsberichterstellung, Multi-Factor Authentication (MFA) und sicheren Zugriff für Ihre mobilen Mitarbeiter.

Office 365-Abonnements enthalten zusätzliche Azure Active Directory-Features. Diese werde in der nachfolgenden Vergleichstabelle beschrieben.


> [AZURE.NOTE]Informationen zu den Preisoptionen für diese Editionen finden Sie unter [Azure Active Directory – Preise](https://azure.microsoft.com/pricing/details/active-directory/). <br>Azure Active Directory Premium und Azure Active Directory Basic werden derzeit in China nicht unterstützt. Wenn Sie weitere Informationen benötigen, kontaktieren Sie uns im Azure Active Directory-Forum.


- **Azure Active Directory Basic** – Diese Edition bietet Sachbearbeitern mit wichtigen Cloudanforderungen auf die Cloud ausgerichtete Lösungen für den Anwendungszugriff und die Self-Service-Identitätsverwaltung. Mit der Basic Edition von Azure Active Directory erhalten Sie Funktionen zur Produktivitätssteigerung und Kostensenkung wie gruppenbasierte Zugriffsverwaltung, Self-Service-Kennwortzurücksetzung für Cloud-Anwendungen und den Azure Active Directory-Anwendungsproxy (zur Veröffentlichung lokaler Webanwendungen mit Azure Active Directory). Diese Features basieren auf einer SLA auf Unternehmensebene mit einer Verfügbarkeitsgarantie von 99,9 Prozent.
 
- **Azure Active Directory Premium** – Azure Active Directory Premium Edition stellt Organisationen mit anspruchsvollen Identitäts- und Zugriffsverwaltungsanforderungen umfangreiche Identitätsverwaltungsfunktionen auf Unternehmensebene zur Verfügung. Darüber hinaus ermöglicht sie Hybridbenutzern den nahtlosen Zugriff auf lokale Funktionen und Cloudfunktionen. Diese Edition enthält alles, was Sie für Information-Worker und Identitätsadministratoren in Hybridumgebungen für Anwendungszugriff, Self-Service-Identitäts- und Zugriffsverwaltung (Identity and Access Management, IAM), Identitätsschutz und Sicherheit in der Cloud benötigen. Sie unterstützt erweiterte Verwaltungs- und Delegierungsressourcen wie dynamische Gruppen und Self-Service-Gruppenverwaltung. Die Edition enthält Microsoft Identity Manager (eine lokale Identitäts- und Zugriffsverwaltungs-Suite) und bietet Cloudfunktionen für das Zurückschreiben. Dadurch werden Lösungen wie die Self-Service-Kennwortzurücksetzung für Ihre lokalen Benutzer ermöglicht.

Wenn Sie sich noch heute für Active Directory Premium registrieren und Ihre Arbeit mit dieser Edition beginnen möchten, gehen Sie zu [Erste Schritte mit Azure Active Directory Premium](active-directory-get-started-premium.md).


> [AZURE.NOTE]Eine Reihe von Azure Active Directory-Funktionen sind als Editionen mit nutzungsbasierter Bezahlung verfügbar:
>
>- Active Directory B2C ist die Identitäts- und Zugriffsverwaltungslösung für Ihre kundenorientierten Anwendungen. Weitere Informationen finden Sie unter [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).
 
>-	Azure Multi-Factor Authentication kann über benutzer- oder authentifizierungsspezifische Anbieter genutzt werden. Weitere Informationen finden Sie unter [Was ist Azure Multi-Factor Authentication?](multi-factor-authentication.md).


<br>

| Featuretyp| Merkmale| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| --- | --- | --- | --- | --- | --- |
| **Gemeinsame Features**| Verzeichnisobjekte [1]| Bis zu 500.000 Objekte| Keine Einschränkung des Objektvolumens| Keine Einschränkung des Objektvolumens| Keine Einschränkung des Objektvolumens für Office 365-Benutzerkonten|
| | [Benutzer- und Gruppenverwaltung (hinzufügen, aktualisieren, löschen), benutzerbasierte Bereitstellung](active-directory-administer.md), [Geräteregistrierung](active-directory-conditional-access-device-registration-overview.md)| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|
| | [SSO für SaaS-Apps, benutzerdefinierte Apps, Anwendungsproxy-Apps](active-directory-enable-sso-scenario.md)| 10 Apps pro Benutzer [2]| 10 Apps pro Benutzer [2]| Unbegrenzt| 10 Apps pro Benutzer [2]|
| | [Self-Service-Kennwortänderung für Cloudbenutzer](active-directory-passwords-update-your-own-password.md)| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|
| | [Connect – Für die Synchronisierung zwischen lokalen Verzeichnissen und Azure Active Directory](active-directory-aadconnect.md)| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|
| | **Vorschau**: [B2B-Zusammenarbeit](active-directory-b2b-collaboration-overview.md)| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|
| | [Sicherheits- und Nutzungsberichte](active-directory-view-access-usage-reports.md)| Einfache Berichte| Einfache Berichte| Erweiterte Berichte| Einfache Berichte|
| **Features der Premium und Basic Edition**| [Gruppenbasierte Anwendungszugriffsverwaltung und Bereitstellung von Anwendungen](active-directory-accessmanagement-group-saasapps.md)| | ![Prüfen][12]| ![Prüfen][12]| |
| | [Self-Service-Kennwortrücksetzung für Cloudbenutzer](active-directory-passwords.md)| | ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|
| | [Unternehmensbranding (Anpassen von Anmeldeseiten und Zugriffsbereich)](active-directory-add-company-branding.md)| | ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|
| | [Anwendungsproxy](active-directory-application-proxy-get-started.md)| | ![Prüfen][12]| ![Prüfen][12]| |
| | [In der SLA garantierte Verfügbarkeit von 99,9 %](https://azure.microsoft.com/support/legal/sla/)| | ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|
| **Nur Premium-Features**| Self-Service-Gruppenverwaltung, Self-Service-Hinzufügung von Anwendungen, dynamische Gruppenmitgliedschaft| | | ![Prüfen][12]| |
| | [Self-Service-Kennwortzurücksetzung, -änderung, -entsperrung mit lokalem Rückschreiben](active-directory-passwords-getting-started.md/#enable-users-to-reset-or-change-their-ad-passwords)| | | ![Prüfen][12]| |
| | [Multi-Factor Authentication (cloudbasiert und lokal)](multi-factor-authentication.md)| | | ![Prüfen][12]| Für Office 365-Apps nur auf die Cloud beschränkt|
| | [Benutzerlizenzen für Microsoft Identity Manager (MIM) sowie MIM-Server [3]](http://www.microsoft.com/server-cloud/products/microsoft-identity-manager/default.aspx)| | | ![Prüfen][12]| |
| | [Cloud-App-Ermittlung](active-directory-cloudappdiscovery-whatis.md)| | | ![Prüfen][12]| |
| | [Azure Active Directory Connect Health](active-directory-aadconnect-health.md)| | | ![Prüfen][12]| |
| | Automatisches Kennwortrollover für Gruppenkonten| | | ![Prüfen][12]| |
| | **Vorschau**: Bedingter Zugriff| | | ![Prüfen][12]| |
| | **Vorschau**: Privileged Identity Management| | | ![Prüfen][12]| |
| **Features für Windows 10 und Azure AD Join**| Einbinden von Windows 10-Geräten in Azure AD, Desktop-SSO, Microsoft Passport für Azure AD, BitLocker-Wiederherstellung (Administrator)| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|
| | Automatische Registrierung für MDM, BitLocker-Wiederherstellung (Self-Service), zusätzliche lokale Administratoren für Windows 10-Geräte über die Einbindung in Azure AD| | | ![Prüfen][12]| |





[1] Das standardmäßige Nutzungskontingent liegt bei 150.000 Objekten. Bei einem Objekt handelt es sich um einen Eintrag im Verzeichnisdienst, der durch seinen eindeutigen Distinguished Name dargestellt wird. Ein Beispiel eines Objekts ist ein Benutzereintrag, der für Authentifizierungszwecke verwendet wird. Falls Sie dieses Standardkontingent überschreiten müssen, wenden Sie sich bitte an den Support. Die Beschränkung auf 500.000 Objekte gilt nicht für Office 365, Microsoft Intune oder andere bezahlte Microsoft-Onlinedienste, die von Azure Active Directory als Verzeichnisdienst abhängig sind.

[2] Mit Azure AD Free und Azure AD Basic können Endbenutzer mit zugewiesenem Zugriff auf SaaS-Apps bis zu 10 Apps in ihrem Zugriffsbereich anzeigen und über SSO auf diese zugreifen. Bei den Editionen „Free“ and „Basic“ können Administratoren SSO konfigurieren und einen Benutzerzugriff auf eine beliebige Anzahl von SaaS-Apps einrichten. Im Zugriffsbereich der Endbenutzer werden jedoch jeweils nur maximal 10 Apps angezeigt.

[3] Rechte an Microsoft Identity Manager Server-Software werden mit Windows Server-Lizenzen gewährt (beliebige Edition). Da Microsoft Identity Manager auf dem Windows Server-Betriebssystem ausgeführt wird, kann Microsoft Identity Manager installiert und auf dem Server verwendet werden, solange auf diesem Server eine gültige, lizenzierte Kopie von Windows Server vorhanden ist. Für Microsoft Identity Manager Server ist keine andere separate Lizenz erforderlich.



## Nächste Schritte

- [Erste Schritte mit Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md)
- [Anzeigen Ihrer Zugriffs- und Nutzungsberichte](active-directory-view-access-usage-reports.md)


<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

<!---HONumber=AcomDC_1210_2015-->