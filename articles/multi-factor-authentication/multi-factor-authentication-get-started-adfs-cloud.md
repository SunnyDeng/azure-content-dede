<properties 
	pageTitle="Sichern von Cloud-Ressourcen mit Azure Multi-Factor Authentication und AD FS" 
	description="Auf dieser Seite zur Azure Multi-Factor Authentication werden die ersten Schritte mit Azure MFA und AD FS in der Cloud beschrieben." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Sichern von Cloud-Ressourcen mit Azure Multi-Factor Authentication und AD FS

Wenn Ihre Organisation über einen Verbund mit Azure Active Directory verfügt und Ressourcen vorhanden sind, auf die von Azure AD zugegriffen wird, können Sie Multi-Factor Authentication oder Active Directory Federation Services zum Sichern dieser Ressourcen verwenden. Führen Sie die Verfahren unten aus, um Azure Active Directory-Ressourcen mit Azure Multi-Factor Authentication oder Active Directory-Verbunddiensten zu sichern.

## So sichern Sie Azure AD-Ressourcen mit AD FS 



1. Führen Sie die unter [Aktivieren der Multi-Factor Authentication](multi-factor-authentication-get-started-cloud/#turn-on-multi-factor-authentication-for-users) beschriebenen Schritte für Benutzer aus, um ein Konto zu aktivieren.
2. Gehen Sie wie folgt vor, um eine Anspruchsregel einzurichten:

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)</center>

- 	Öffnen Sie die AD FS-Verwaltungskonsole.
- 	Navigieren Sie zu "Vertrauensstellungen der vertrauenden Seite", und klicken Sie mit der rechten Maustaste auf "Vertrauensstellungen der vertrauenden Seite". Wählen Sie "Anspruchsregeln bearbeiten" aus.
- 	Klicken Sie auf "Regel hinzufügen".
- 	Wählen Sie in der Dropdownliste "Ansprüche per benutzerdefinierter Regel senden" aus, und klicken Sie auf "Weiter".
- 	Geben Sie einen Namen für die Anspruchsregel ein.
- 	Fügen Sie Folgendes unter "Benutzerdefinierte Regel" hinzu:


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	Entsprechender Anspruch:

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- Klicken Sie auf OK. Klicken Sie auf Fertig stellen. Schließen Sie die AD FS-Verwaltungskonsole.

Benutzer können dann die Anmeldung mithilfe der lokalen Methode (z. B. Smartcard) abschließen.


 

<!---HONumber=July15_HO2-->