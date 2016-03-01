<properties
	pageTitle="Sichern des Zugriffs auf Office 365 und andere mit Azure Active Directory verbundene Apps | Microsoft Azure"  
    description="Mit der bedingten Zugriffssteuerung überprüft Azure Active Directory die besonderen Bedingungen, die Sie beim Authentifizieren des Benutzers und vor dem Gewähren des Zugriffs auf die Anwendung auswählen. Nachdem diese Bedingungen erfüllt sind, wird der Benutzer authentifiziert und erhält Zugriff auf die Anwendung."  
    services="active-directory" 
	keywords="Zugriff auf Apps, sicherer Zugriff auf Unternehmensressourcen, Richtlinien für bedingten Zugriff" 
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="02/10/2016"
	ms.author="femila"/>


# Sichern des Zugriffs auf Office 365 und andere mit Azure Active Directory verbundene Apps  
  
In jedem Unternehmen ist das Sichern des Zugriffs auf Unternehmensressourcen von größter Bedeutung. Mit der Einführung von Clouddiensten und mobilen Geräten hat sich der Zugriff der Benutzer auf Unternehmensressourcen maßgeblich geändert. Dadurch werden Änderungen an der Strategie zum Sichern von Unternehmensressourcen erforderlich.
  
## Gründe für den bedingten Zugriff  
 Die Steuerungsfunktionen für den bedingten Zugriff in Azure Active Directory bieten einfache Methoden, mit denen Unternehmen ihre Ressourcen sowohl in der Cloud als auch lokal sichern können. Wenn Sie beispielsweise den Zugriff auf Ihre Ressourcen über ein gestohlenes Kennwort verhindern oder für den Zugriff auf Unternehmensinhalte ein zuverlässiges, verwaltetes Gerät durchsetzen möchten, ist Azure Active Directory genau das Richtige.

## Wie wird die bedingte Zugriffskontrolle durchgesetzt?  
 Mit der bedingten Zugriffssteuerung überprüft Azure Active Directory beim Authentifizieren des Benutzers die von Ihnen ausgewählten besonderen Bedingungen, bevor der Zugriff auf eine Anwendung gewährt wird. Nachdem diese Bedingungen erfüllt sind, wird der Benutzer authentifiziert und erhält Zugriff auf die Anwendung.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Benutzerbasierter Zugriffs auf Ressourcen
  
- **Benutzerattribute**: Auf Ebene der Benutzerattribute können Sie Richtlinien anwenden, um sicherzustellen, dass nur autorisierte Benutzer auf Unternehmensressourcen zugreifen können.   
- **Gruppenmitgliedschaft eines Benutzers**: Sie können die Zugriffsebene für einen Benutzer auch über dessen Mitgliedschaft in einer oder mehreren Gruppen steuern.   
- **Multi-Factor Authentication (MFA)**: Darüber hinaus können Sie auch Richtlinien durchsetzen, bei denen ein Benutzer seine Identität über ein mehrstufiges Authentifizierungssystem authentifizieren muss. Beispielsweise können Sie einen Benutzer zum Bestätigen einer PIN auf einem persönlichen Mobiltelefon zwingen, um eine zusätzliche Sicherheitsebene zu gewährleisten. Die MFA schützt Ihre Ressourcen vor dem Zugriff durch einen nicht autorisierten Benutzer, der Zugriff auf den Benutzernamen und das Kennwort eines gültigen Benutzers erlangt hat. 

## Gerätebasierter bedingter Zugriff 

- **Registrierte Geräte**: Auf der Geräteebene können Sie Richtlinien festlegen, die erzwingen, dass nur registrierte oder bekannte Geräte zugreifen dürfen. Darüber hinaus können Sie eine Lösung für die mobile Geräteverwaltung (MDM) wie Microsoft Intune verwenden, um sicherzustellen, dass nur verwaltete Geräte auf Ihre Ressourcen zugreifen. Durch den bedingten Zugriff auf Geräteebene wird sichergestellt, dass nur solche Geräte Zugriff erhalten, die mit Ihren Richtlinien kompatibel sind, z. B. mit dem Erzwingen der PIN auf einem Gerät. Darüber hinaus können Sie mit MDM-Lösungen sicherstellen, dass Unternehmensdaten auf einem verloren gegangenen/gestohlenen Gerät remote gelöscht werden können.  
- **Geräterichtlinien**: Sie können auch Richtlinien festlegen, um den Zugriff nur pro Anwendung einzuschränken. Darüber hinaus können Sie die Zugriffsebene basierend auf dem physischen Standort des Geräts einstellen, d. h., ob die Anforderung von einem bekannten Unternehmensnetzwerk oder von außerhalb stammt.  

Die Zugriffsebene, die Sie mit diesen Richtlinien festlegen können, können auf Ressourcen in der Cloud oder lokal angewendet werden. Die Ressourcen in der Cloud könnten z. B. Apps wie Office 365 und SaaS-Apps von Drittanbietern sein. Ferner kann es sich um branchenspezifische Apps handeln, die Sie in der Cloud gehostet haben.
  
## Bedingter Zugriff – eine Inhaltszuordnung  
In der folgenden Inhaltszuordnung werden Dokumente aufgeführt, in denen Sie weitere Informationen über das Aktivieren des bedingten Zugriffs in Ihrer aktuellen Bereitstellung finden.


| Szenario | Artikel |
|------------------------------------------------------|----------|
| Schützen von Unternehmensressourcen vor Phishing-Angriffen |[Erste Schritte mit bedingtem Zugriff auf AAD-SaaS-Apps mit MFA und Extranet](active-directory-conditional-access-azuread-connected-apps.md)<br><br>[Bedingter Zugriff auf Azure AD-Apps](active-directory-conditional-access-technical-reference.md)<br><br>[Konfigurieren von MFA](multi-factor-authentication-get-started-cloud.md)<br><br>[Sichern von Cloudressourcen mit Azure Multi-Factor Authentication und AD FS](https://technet.microsoft.com/library/dn758113.aspx)<br><br>[Aspekte zu MFA auf Benutzerebene](multi-factor-authentication-end-user-manage-settings.md)<br><br>[MFA über Extranet](multi-factor-authentication-get-started-adfs-cloud.md)|
| Schützen von Unternehmensdaten auf verloren gegangenen/gestohlenen Geräten |[Geräteregistrierungsdienst](active-directory-conditional-access-device-registration-overview.md)<br><br> [Registrieren von in eine Domäne eingebundenen Geräten](active-directory-azureadjoin-setup.md)<br><br> [Verwenden von in Azure AD registrierten Geräten für den lokalen bedingten Zugriff](active-directory-conditional-access-on-premises-setup.md) <br><br>[Konfigurieren der automatischen Geräteregistrierung für in eine Domäne eingebundene Windows 7-Geräte](active-directory-conditional-access-automatic-device-registration-windows7.md) <br><br>[Konfigurieren der automatischen Geräteregistrierung für in eine Domäne eingebundene Windows 8.1-Geräte](active-directory-conditional-access-automatic-device-registration-windows8_1.md) <br><br>[Geräterichtlinien für bedingten Zugriff für Office 365-Dienste](active-directory-conditional-access-device-policies.md)|
| Additional Info |[Häufig gestellte Fragen zum bedingten Zugriff](active-directory-conditional-faqs.md)|

<!---HONumber=AcomDC_0218_2016-->