<properties
	pageTitle="Azure Active Directory-Editionen| Microsoft Azure"
	description="In diesem Thema werden die Features und Unterschiede der kostenlosen und kostenpflichtigen Editionen von Azure Active Directory beschrieben. Azure Active Directory Basic ist die kostenlose und Azure Active Directory Premium die kostenpflichtige Edition."
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
	ms.date="02/16/2016"
	ms.author="markvi"/>

# Azure Active Directory-Editionen

Alle Microsoft Online-Unternehmensdienste nutzen für Anmelde- und andere Identitätsanforderungen Azure Active Directory. Wenn Sie einen der Microsoft Online-Unternehmensdienste (z. B. Office 365 oder Microsoft Azure) abonnieren, erhalten Sie Azure Active Directory (Azure AD) mit Zugriff auf alle unten beschriebenen Free-Features.


Azure Active Directory ist ein Dienst, der Ihren Mitarbeitern, Partnern und Kunden umfassende Funktionen zur Identitäts- und Zugriffsverwaltung in der Cloud bietet. Er kombiniert Verzeichnisdienste, eine erweiterte Identitätskontrolle, eine standardbasierte Plattform für Entwickler mit umfangreichen Funktionen und Zugriffsverwaltung für eigene oder tausende vorab integrierte Anwendungen. Mit der Free-Edition von Azure Active Directory können Sie Benutzer und Gruppen verwalten, eine Synchronisierung mit lokalen Verzeichnissen ausführen, sich mittels einmaligem Anmelden (SSO) bei Azure, Office 365 und tausenden gängigen SaaS-Anwendungen wie Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox und mehr anmelden. Weitere Informationen zu Azure Active Directory finden Sie unter [Was ist Azure AD?](active-directory-whatis.md).



Sie können Ihre Azure Active Directory-Anwendung erweitern, indem Sie mit Azure Active Directory Basic und Premium kostenpflichtige Funktionen hinzufügen. Die kostenpflichtigen Editionen von Azure Active Directory ergänzen Ihr kostenloses Verzeichnis. Sie bieten leistungsstarke Funktionen wie etwa Self-Service, optimierte Überwachung, Sicherheitsberichterstellung, Multi-Factor Authentication (MFA) und sicheren Zugriff für Ihre mobilen Mitarbeiter.

Office 365-Abonnements enthalten zusätzliche Azure Active Directory-Features. Diese werde in der nachfolgenden Vergleichstabelle beschrieben.


> [AZURE.NOTE] Informationen zu den Preisoptionen für diese Editionen finden Sie unter [Azure Active Directory – Preise](https://azure.microsoft.com/pricing/details/active-directory/). <br>Azure Active Directory Premium und Azure Active Directory Basic werden derzeit in China nicht unterstützt. Wenn Sie weitere Informationen benötigen, kontaktieren Sie uns im Azure Active Directory-Forum.


- **Azure Active Directory Basic** – Diese Edition bietet Sachbearbeitern mit wichtigen Cloudanforderungen auf die Cloud ausgerichtete Lösungen für den Anwendungszugriff und die Self-Service-Identitätsverwaltung. Mit der Basic Edition von Azure Active Directory erhalten Sie Funktionen zur Produktivitätssteigerung und Kostensenkung wie gruppenbasierte Zugriffsverwaltung, Self-Service-Kennwortzurücksetzung für Cloud-Anwendungen und den Azure Active Directory-Anwendungsproxy (zur Veröffentlichung lokaler Webanwendungen mit Azure Active Directory). Diese Features basieren auf einer SLA auf Unternehmensebene mit einer Verfügbarkeitsgarantie von 99,9 Prozent.
 
- **Azure Active Directory Premium** – Azure Active Directory Premium Edition stellt Organisationen mit anspruchsvollen Identitäts- und Zugriffsverwaltungsanforderungen umfangreiche Identitätsverwaltungsfunktionen auf Unternehmensebene zur Verfügung. Darüber hinaus ermöglicht sie Hybridbenutzern den nahtlosen Zugriff auf lokale Funktionen und Cloudfunktionen. Diese Edition enthält alles, was Sie für Information-Worker und Identitätsadministratoren in Hybridumgebungen für Anwendungszugriff, Self-Service-Identitäts- und Zugriffsverwaltung (Identity and Access Management, IAM), Identitätsschutz und Sicherheit in der Cloud benötigen. Sie unterstützt erweiterte Verwaltungs- und Delegierungsressourcen wie dynamische Gruppen und Self-Service-Gruppenverwaltung. Die Edition enthält Microsoft Identity Manager (eine lokale Identitäts- und Zugriffsverwaltungs-Suite) und bietet Cloudfunktionen für das Zurückschreiben. Dadurch werden Lösungen wie die Self-Service-Kennwortzurücksetzung für Ihre lokalen Benutzer ermöglicht.

Wenn Sie sich noch heute für Active Directory Premium registrieren und Ihre Arbeit mit dieser Edition beginnen möchten, gehen Sie zu [Erste Schritte mit Azure Active Directory Premium](active-directory-get-started-premium.md).


> [AZURE.NOTE] 
Eine Reihe von Azure Active Directory-Funktionen sind als Editionen mit nutzungsbasierter Bezahlung verfügbar:
>
>- Active Directory B2C ist die Identitäts- und Zugriffsverwaltungslösung für Ihre kundenorientierten Anwendungen. Weitere Informationen finden Sie unter [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).
 
>-	Azure Multi-Factor Authentication kann über benutzer- oder authentifizierungsspezifische Anbieter genutzt werden. Weitere Informationen finden Sie unter [Was ist Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md).


##Vergleichen der allgemein verfügbaren Features

> [AZURE.NOTE] Eine andere Ansicht dieser Daten finden Sie unter [Azure Active Directory-Funktionen](https://www.microsoft.com/server-cloud/products/azure-active-directory/Features.aspx).

| | Azure AD Free | Azure AD Basic | Azure AD Premium |
| ---                      | :-:           | :-:            | :-:              |
| Gemeinsame Features | ![Prüfen][12] | ![Prüfen][12] | ![Prüfen][12] |
| Basic-Features | | ![Prüfen][12] | ![Prüfen][12] |
| Premium-Features | | | ![Prüfen][12] |



<br>

**Gemeinsame Features**

- [Verzeichnisobjekte](#directory-objects) 

- [Benutzer-/Gruppenverwaltung (hinzufügen/aktualisieren/löschen)/Benutzerbasierte Bereitstellung, Geräteregistrierung](#usergroup-management-addupdatedelete-user-based-provisioning-device-registration)

- [Einmaliges Anmelden (Single Sign-On, SSO)](#single-sign-on-sso)

- [Self-Service-Kennwortänderung für Cloudbenutzer](#self-service-password-change-for-cloud-users)

- [Connect (Synchronisierungsmodul, mit dem lokale Verzeichnisse auf Azure Active Directory ausgeweitet werden)](#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory)

- [Sicherheits- und Nutzungsberichte](#securityusage-reports)



**Basic-Features**

- [Gruppenbasierte Zugriffsverwaltung/Bereitstellung](#group-based-access-managementprovisioning)

- [Self-Service-Kennwortrücksetzung für Cloudbenutzer](#self-service-password-reset-for-cloud-users)

- [Unternehmensbranding (Anpassen von Anmeldeseiten/Zugriffsbereich)](#company-branding-logon-pagesaccess-panel-customization)

- [Anwendungsproxy](#application-proxy)

- [SLA 99,9 %](#sla-999)


**Premium-Features**

- [Self-Service-Verwaltung von Gruppen und Apps/Self-Service-Hinzufügung von Anwendungen/Dynamische Gruppen](#self-service-group-and-app-managementself-service-application-additions-dynamic-groups)

- [Self-Service-Kennwortzurücksetzung, -änderung, -entsperrung mit lokalem Rückschreiben](#self-service-password-resetchangeunlock-with-on-premises-write-back)

- [Multi-Factor Authentication (cloudbasiert und lokal [MFA-Server])](#multi-factor-authentication-cloud-and-on-premises-mfa-server)

- [MIM-CAL und MIM-Server](#mim-cal-mim-server)

- [Cloud-App-Ermittlung](#cloud-app-discovery)

- [Connect Health](#connect-health)

- [Automatisches Kennwortrollover für Gruppenkonten](#automatic-password-rollover-for-group-accounts)


**Azure Active Directory-Einbindung – nur auf Windows 10 bezogene Features**

- [Verbinden Sie ein Gerät mit Azure AD, Desktop-SSO, Microsoft Passport für Azure AD, BitLocker-Wiederherstellung (Administrator)](#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery)

- [Automatische Registrierung für MDM, BitLocker-Wiederherstellung (Self-Service), zusätzliche lokale Administratoren für Windows 10-Geräte über die Einbindung in Azure AD](#mdm-auto-enrolment-self-service-bitlocker-recovery-additional-local-administrators-to-windows-10-devices-via-azure-ad-join)




<br> <hr>
## Gemeinsame Features
#### Verzeichnisobjekte 

**Typ:** Gemeinsame Features

Das standardmäßige Nutzungskontingent liegt bei 150.000 Objekten. Bei einem Objekt handelt es sich um einen Eintrag im Verzeichnisdienst, der durch seinen eindeutigen Distinguished Name dargestellt wird. Ein Beispiel eines Objekts ist ein Benutzereintrag, der für Authentifizierungszwecke verwendet wird. Falls Sie dieses Standardkontingent überschreiten müssen, wenden Sie sich bitte an den Support. Die Kontingentgrenze von 500.000 Objekten gilt nicht für Office 365, Microsoft Intune oder einen anderen zahlungspflichtigen Microsoft-Onlinedienst, der Azure Active Directory als Verzeichnisdienst nutzt.


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| Bis zu 500.000 Objekte| Keine Einschränkung des Objektvolumens| Keine Einschränkung des Objektvolumens| Keine Einschränkung des Objektvolumens für Office 365-Benutzerkonten|


<br>

#### Benutzer-/Gruppenverwaltung (hinzufügen/aktualisieren/löschen)/Benutzerbasierte Bereitstellung, Geräteregistrierung

**Typ:** Gemeinsame Features

**Verfügbarkeit:**


| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|

**Weitere Informationen:**

- [Verwalten Ihres Azure AD-Verzeichnisses](active-directory-administer.md)
- [Azure Active Directory-Geräteregistrierung – Übersicht](active-directory-conditional-access-device-registration-overview.md)


<br> <hr>
#### Einmaliges Anmelden (Single Sign-On, SSO)

**Typ:** Gemeinsame Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| 10 Apps pro Benutzer [1] <br>(bereits integrierte SaaS-Apps und von Entwicklern integrierte Apps)| 10 Apps pro Benutzer [1] <br> (Free-Tarif und Anwendungsproxy-Apps) | Keine Begrenzung [2] <br> (Free-, Basic-Tarif und Vorlagen für Self-Service-App-Integration)| 10 Apps pro Benutzer [1] <br> (bereits integrierte SaaS-Apps und von Entwicklern integrierte Apps)|

[1] Mit Azure AD Free und Azure AD Basic können Endbenutzer mit zugewiesenem Zugriff auf SaaS-Apps bis zu 10 Apps in ihrem Zugriffsbereich anzeigen und über SSO auf diese zugreifen. Bei den Editionen „Free“ und „Basic“ können Administratoren SSO konfigurieren und einen Benutzerzugriff auf eine beliebige Anzahl von SaaS-Apps einrichten. Im Zugriffsbereich der Endbenutzer werden jedoch jeweils nur maximal 10 Apps angezeigt.

[2] Self-Service-Integration jeder Anwendung, die SAML, SCIM oder formularbasierte Authentifizierung unterstützt, mithilfe von Vorlagen im Anwendungskatalogmenü. Weitere Informationen finden Sie unter [Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure Active Directory-Anwendungskatalog enthalten sind](active-directory-saas-custom-apps.md)

**Weitere Informationen:**

- [Verwalten von Anwendungen mit Azure Active Directory (AD)](active-directory-enable-sso-scenario.md)

<br> <hr>
#### Self-Service-Kennwortänderung für Cloudbenutzer

**Typ:** Gemeinsame Features

**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|

**Weitere Informationen:**

- [Aktualisieren Ihres eigenen Kennworts](active-directory-passwords-update-your-own-password.md)


<br> <hr>
#### Connect (Synchronisierungsmodul, mit dem lokale Verzeichnisse auf Azure Active Directory ausgeweitet werden) 

**Typ:** Gemeinsame Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|

**Weitere Informationen:**

- [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<br> <hr>
#### Sicherheits-/Nutzungsberichte

**Typ:** Gemeinsame Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| 3 einfache Berichte| 3 einfache Berichte| Erweiterte Berichte| 3 einfache Berichte|

**Weitere Informationen:**

- [Anzeigen Ihrer Zugriffs- und Nutzungsberichte](active-directory-view-access-usage-reports.md)

<br> <hr>

## Features der Premium und Basic Edition
#### Gruppenbasierte Zugriffsverwaltung/Bereitstellung

**Typ:** Basic-Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| | ![Prüfen][12]| ![Prüfen][12]| |

**Weitere Informationen:**

- [Verwenden einer Gruppe zum Verwalten des Zugriffs auf SaaS-Anwendungen](active-directory-accessmanagement-group-saasapps.md)

<br> <hr>
#### Self-Service-Kennwortrücksetzung für Cloudbenutzer

**Typ:** Basic-Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| | ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|

**Weitere Informationen:**

- [Zurücksetzen des Azure AD-Kennworts für Benutzer und Administratoren](active-directory-passwords.md)

<br> <hr>
#### Unternehmensbranding (Anpassen von Anmeldeseiten/Zugriffsbereich)

**Typ:** Basic-Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| | ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|

**Weitere Informationen:**

- [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md)

<br> <hr>
#### Anwendungsproxy

**Typ:** Basic-Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| | ![Prüfen][12]| ![Prüfen][12]| |

**Weitere Informationen:**

- [Bereitstellen von sicherem Remotezugriff auf lokale Anwendungen](active-directory-application-proxy-get-started.md)

<br> <hr>
#### SLA 99,9 %

**Typ:** Basic-Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| | ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|

**Weitere Informationen:**

- [Vereinbarungen zum Servicelevel (SLAs)](https://azure.microsoft.com/support/legal/sla/)

<br> <hr>

## Premium-Features
#### Self-Service-Verwaltung von Gruppen und Apps/Self-Service-Hinzufügung von Anwendungen/Dynamische Gruppen

**Typ:** Premium-Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| | | ![Prüfen][12]| |



<br>
         
#### Self-Service-Kennwortzurücksetzung, -änderung, -entsperrung mit lokalem Rückschreiben

**Typ:** Premium-Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| | | ![Prüfen][12]| |



<br>

#### Multi-Factor Authentication (cloudbasiert und lokal [MFA-Server])

**Typ:** Premium-Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| | | ![Prüfen][12]| Für Office 365-Apps nur auf die Cloud beschränkt|

**Weitere Informationen:**

- [Was ist Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)

<br> <hr>
#### MIM-CAL und MIM-Server 

Rechte an Microsoft Identity Manager Server-Software werden mit Windows Server-Lizenzen gewährt (beliebige Edition). Da Microsoft Identity Manager auf dem Windows Server-Betriebssystem ausgeführt wird, kann Microsoft Identity Manager installiert und auf dem Server verwendet werden, solange auf diesem Server eine gültige, lizenzierte Kopie von Windows Server vorhanden ist. Für Microsoft Identity Manager Server ist keine andere separate Lizenz erforderlich.

**Typ:** Premium-Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| | | ![Prüfen][12]| |



<br>

#### Cloud-App-Ermittlung 

**Typ:** Premium-Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| | | ![Prüfen][12]| |

**Weitere Informationen:**

- [Suchen nach nicht verwalteten Cloudanwendungen per Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)

<br> <hr>
#### Connect Health

**Typ:** Premium-Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| | | ![Prüfen][12]| |

**Weitere Informationen:**

- [Überwachen Ihrer lokalen Identitätsinfrastruktur und Synchronisierung von Diensten in der Cloud](active-directory-aadconnect-health.md)

<br> <hr>
#### Automatisches Kennwortrollover für Gruppenkonten

**Typ:** Premium-Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| | | ![Prüfen][12]| |


<br> <hr>
## Azure Active Directory-Einbindung – nur auf Windows 10 bezogene Features
#### Verbinden Sie ein Gerät mit Azure AD, Desktop-SSO, Microsoft Passport für Azure AD, BitLocker-Wiederherstellung (Administrator)

**Typ:** Azure Active Directory-Einbindung – nur auf Windows 10 bezogene Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]| ![Prüfen][12]|


<br>

#### Automatische Registrierung für MDM, BitLocker-Wiederherstellung (Self-Service), zusätzliche lokale Administratoren für Windows 10-Geräte über die Einbindung in Azure AD

**Typ:** Azure Active Directory-Einbindung – nur auf Windows 10 bezogene Features


**Verfügbarkeit:**

| Free Edition| Basic Edition| Premium Edition| Nur für Office 365-Apps |
| :-: | :-: | :-: | :-: |
| | | ![Prüfen][12]| |

<hr>


## Azure AD-Vorschaufunktionen
Neben den allgemein verfügbaren Funktionen der Free, Basic und Premium Editionen bietet Azure AD auch eine Sammlung von Vorschaufunktionen. Die Vorschaufunktionen vermitteln Ihnen einen Eindruck von in Kürze verfügbaren Funktionen, und Sie können ermitteln, ob diese Funktionen hilfreich für die Verbesserung Ihrer Umgebung sind.

**Verfügbare Vorschaufunktionen:**

- [B2B-Zusammenarbeit](active-directory-b2b-collaboration-overview.md)
- Bedingter Zugriff
- [Verwaltungseinheiten](active-directory-administrative-units-management.md)
- Privileged Identity Management
- [HR-Anwendungsintegration](active-directory-saas-workday-inbound-tutorial.md)





## Nächste Schritte

- [Erste Schritte mit Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md)
- [Anzeigen Ihrer Zugriffs- und Nutzungsberichte](active-directory-view-access-usage-reports.md)


<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

<!---HONumber=AcomDC_0218_2016-->