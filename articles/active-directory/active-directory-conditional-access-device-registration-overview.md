<properties
	pageTitle="Azure Active Directory-Geräteregistrierung – Übersicht | Microsoft Azure"
	description="Stellt die Grundlage für gerätebasierte Szenarien mit bedingtem Zugriff dar. Wenn ein Gerät registriert wurde, stellt die Azure Active Directory-Geräteregistrierung eine Identität für das Gerät bereit, die bei der Benutzeranmeldung zum Authentifizieren des Geräts dient."
	services="active-directory"
	keywords="Geräteregistrierung, Geräteregistrierung aktivieren, Geräteregistrierung und MDM"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/29/2016"
	ms.author="femila"/>

# Erste Schritte bei der Azure Active Directory-Geräteregistrierung

Die Azure Active Directory-Geräteregistrierung ist die Grundlage gerätebasierter Szenarien für den bedingten Zugriff. Wenn ein Gerät registriert wird, stellt die Azure Active Directory-Geräteregistrierung eine Identität für das Gerät bereit, die bei der Benutzeranmeldung zum Authentifizieren des Geräts dient. Das authentifizierte Gerät und die Attribute des Geräts können anschließend verwendet werden, um bedingte Zugriffsrichtlinien für Anwendungen zu erzwingen, die in der Cloud und lokal gehostet werden.

In Kombination mit einer Lösung für die Verwaltung mobiler Geräte, wie z.B. Intune, werden die Geräteattribute in Azure Active Directory mit zusätzlichen Informationen über das Gerät aktualisiert. So können Sie Regeln für den bedingten Zugriff erstellen, die erzwingen, dass der Zugriff von Geräten Ihren Standards für Sicherheit und Kompatibilität entspricht.

Die Azure Active Directory-Geräteregistrierung steht in Azure Active Directory zur Verfügung. Dieser Dienst bietet Unterstützung für iOS-, Android- und Windows-Geräte. Für die einzelnen Szenarien, in denen die Geräteregistrierung von Azure Active Directory eingesetzt wird, gelten ggf. spezielle Anforderungen und Einschränkungen hinsichtlich der Plattformunterstützung.

## Unterstützte Szenarien für die Azure Active Directory-Geräteregistrierung

Die Azure Active Directory-Geräteregistrierung unterstützt iOS-, Android- und Windows-Geräte. Für die einzelnen Szenarien, in denen die Geräteregistrierung von Azure Active Directory eingesetzt wird, gelten ggf. spezielle Anforderungen und Einschränkungen hinsichtlich der Plattformunterstützung. Folgende Szenarien sind möglich:

- **Bedingter Zugriff auf lokal gehostete Anwendungen:** Sie können registrierte Geräte mit Zugriffsrichtlinien für Anwendungen verwenden, die für die Verwendung von AD FS mit Windows Server 2012 R2 konfiguriert sind. Weitere Informationen zum Einrichten des bedingten Zugriffs für lokale Systeme finden Sie unter [Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung](active-directory-conditional-access-on-premises-setup.md). 

- **Bedingter Zugriff für Office 365-Anwendungen mit Microsoft Intune:** IT-Administratoren können Geräterichtlinien für den bedingten Zugriff bereitstellen, um Unternehmensressourcen zu schützen, und gleichzeitig Information-Workern auf kompatiblen Geräten den Zugriff auf die Dienste zu gestatten. Weitere Informationen finden Sie unter „Geräterichtlinien für den bedingten Zugriff auf Office 365-Dienste“.
 
##Einrichten der Azure Active Directory-Geräteregistrierung

Sie müssen die Azure AD-Geräteregistrierung im Azure-Portal aktivieren, damit mobile Geräte den Dienst ermitteln können, indem sie nach bekannten DNS-Einträgen suchen. Sie müssen den DNS-Eintrag für Ihr Unternehmen so konfigurieren, dass Windows 10-, Windows 8.1-, Windows 7-, Android- und iOS-Geräte den Dienst ermitteln und verwenden können. Sie können registrierte Geräte mithilfe des Verwaltungsportals in Azure Active Directory anzeigen und aktivieren/deaktivieren.

### Aktivieren des Azure Active Directory-Geräteregistrierungsdiensts

1. Melden Sie sich als Administrator beim Microsoft Azure-Portal an.
2. Wählen Sie im linken Bereich **Active Directory** aus.
3. Wählen Sie auf der Registerkarte **Verzeichnis** Ihr Verzeichnis aus.
4. Wählen Sie die Registerkarte **Konfigurieren** aus.
5. Scrollen Sie zum Abschnitt **Geräte**.
6. Wählen Sie für **Benutzer dürfen Geräte in den Arbeitsplatz einbinden** die Option **Alle** aus.
7. Wählen Sie die maximale Anzahl von Geräten aus, die Sie pro Benutzer autorisieren möchten.

>[AZURE.NOTE]
Die Registrierung mit Microsoft Intune oder der Verwaltung mobiler Geräte für Office 365 erfordert eine Arbeitsbereichsverknüpfung. Wenn Sie einen dieser Dienste konfiguriert haben, wird "Alle" ausgewählt und die Schaltfläche "Keine" deaktiviert.

Standardmäßig ist die zweistufige Authentifizierung für den Dienst nicht aktiviert. Die zweistufige Authentifizierung wird jedoch empfohlen, wenn ein Gerät registriert wird.

- Bevor Sie für diesen Dienst eine zweistufige Authentifizierung anfordern, müssen Sie in Azure Active Directory einen Anbieter für die zweistufige Authentifizierung konfigurieren und Ihre Benutzerkonten für Multi-Factor Authentication (MFA) konfigurieren. Weitere Informationen finden Sie unter [Hinzufügen von Multi-Factor Authentication zu Azure Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

- Wenn Sie die Active Directory-Verbunddienste (AD FS) mit Windows Server 2012 R2 verwenden, müssen Sie in AD FS ein Modul für die zweistufige Authentifizierung konfigurieren. Weitere Informationen finden Sie unter [Verwenden von Multi-Factor Authentication mit den Active Directory-Verbunddiensten](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## Konfigurieren der Ermittlung für die Azure Active Directory-Geräteregistrierung
Windows 7- und Windows 8.1-Geräte ermitteln den Geräteregistrierungsdienst, indem sie den Benutzerkontonamen mit dem Namen eines bekannten Geräteregistrierungsservers kombinieren.

Sie müssen einen DNS CNAME-Eintrag erstellen, der auf den A-Eintrag verweist, der Ihrem Azure Active Directory-Geräteregistrierungsdienst zugeordnet ist. Der CNAME-Eintrag muss das bekannte Präfix "enterpriseregistration" verwenden, gefolgt vom UPN-Suffix, das von den Benutzerkonten in Ihrer Organisation verwendet wird. Wenn Ihre Organisation mehrere UPN-Suffixe verwendet, müssen in DNS mehrere CNAME-Einträge erstellt werden.

Wenn Sie z. B. in Ihrer Organisation zwei UPN-Suffixe namens "@contoso.com" und "@region.contoso.com" verwenden, erstellen Sie die folgenden DNS-Einträge.
 
| Eintrag | Typ | Adresse |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

## Anzeigen und Verwalten von Geräteobjekten in Azure Active Directory
1. Über das Azure-Administratorportal können Sie Geräte anzeigen, blockieren und Blockierungen aufheben. Ein blockiertes Gerät verliert den Zugriff auf Anwendungen, die so konfiguriert sind, dass nur registrierte Geräte für den Zugriff zulässig sind.
2. Melden Sie sich als Administrator beim Microsoft Azure-Portal an.
3. Wählen Sie im linken Bereich **Active Directory** aus.
4. Wählen Sie Ihr Verzeichnis.
5. Klicken Sie auf die Registerkarte **Benutzer**. Wählen Sie anschließend einen Benutzer aus, um die zugehörigen Geräte anzuzeigen.
6. Klicken Sie auf die Registerkarte **Geräte**.
7. Wählen Sie im Dropdownmenü die Einstellung **Registrierte Geräte** aus.
8. Hier können Sie registrierten Geräte für einen Benutzer anzeigen, blockieren oder Blockierungen aufheben. 

## Weitere Themen

Sie können Ihre Windows 7- und Windows 8.1-Geräte, die einer Domäne angehören, mit der Azure AD-Geräteregistrierung registrieren. Das folgende Thema enthält weitere Informationen zu den Voraussetzungen und den zum Konfigurieren der Geräteregistrierung für Windows 7- und Windows 8.1-Geräte erforderlichen Schritte.

- [Automatische Geräteregistrierung mit Azure Active Directory für in Domänen eingebundene Windows-Geräte](active-directory-conditional-access-automatic-device-registration.md) 
- [Konfigurieren der automatischen Geräteregistrierung für in eine Domäne eingebundene Windows 7-Geräte](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Konfigurieren der automatischen Geräteregistrierung für in eine Domäne eingebundene Windows 8.1-Geräte](active-directory-conditional-access-automatic-device-registration-windows8_1.md)
- [Automatische Geräteregistrierung bei Azure Active Directory für in Domänen eingebundene Windows 10-Geräte](active-directory-azureadjoin-devices-group-policy.md)

<!---HONumber=AcomDC_0330_2016-->