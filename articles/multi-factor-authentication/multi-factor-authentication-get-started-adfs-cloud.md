<properties 
	pageTitle="Sichern von Cloud-Ressourcen mit Azure Multi-Factor Authentication und AD FS"
	description="Auf dieser Seite zur Azure Multi-Factor Authentication werden die ersten Schritte mit Azure MFA und AD FS in der Cloud beschrieben."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Sichern von Cloud-Ressourcen mit Azure Multi-Factor Authentication und AD FS

Wenn Ihre Organisation über einen Verbund mit Azure Active Directory verfügt und Ressourcen vorhanden sind, auf die von Azure AD zugegriffen wird, können Sie Multi-Factor Authentication oder Active Directory Federation Services zum Sichern dieser Ressourcen verwenden. Führen Sie die Verfahren unten aus, um Azure Active Directory-Ressourcen mit Azure Multi-Factor Authentication oder Active Directory-Verbunddiensten zu sichern.

## So sichern Sie Azure AD-Ressourcen mit AD FS 



1. Führen Sie die unter [Aktivieren von Multi-Factor Authentication](active-directory/multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users) beschriebenen Schritte für Benutzer aus, um ein Konto zu aktivieren.
2. Gehen Sie wie folgt vor, um eine Anspruchsregel einzurichten:

![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)

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

## Vertrauenswürdige IPs für Partnerbenutzer
Mit vertrauenswürdigen IPs können Administratoren die Multi-Factor Authentication für bestimmte IP-Adressen oder Partnerbenutzer umgehen, deren Anfragen aus dem eigenen Intranet stammen. In den folgenden Abschnitten wird beschrieben, wie Sie vertrauenswürdige IPs für die Azure Multi-Factor Authentication mit Partnerbenutzern konfigurieren und die Multi-Factor Authentication umgehen, wenn eine Anforderung aus dem Intranet eines Partnerbenutzers stammt. Hierzu wird für AD FS die Verwendung eines Passthrough-Elements oder für die Filterung einer Vorlage für einen eingehenden Anspruch mit dem Anspruchstyp „Innerhalb des Unternehmensnetzwerks“ konfiguriert. In diesem Beispiel wird Office 365 für die Vertrauensstellungen der vertrauenden Seite verwendet.

### Konfigurieren der AD FS-Anspruchsregeln

Als Erstes müssen wir die AD FS-Ansprüche konfigurieren. Wir werden zwei Anspruchsregeln erstellen: eine für den Anspruchstyp "Innerhalb des Unternehmensnetzwerks" und eine weitere zur Aufrechterhaltung der Anmeldung von Benutzern.

1. Öffnen Sie die AD FS-Verwaltung.
2. Wählen Sie auf der linken Seite die Option für Vertrauensstellungen für vertrauende Seiten.
3. Klicken Sie in der Mitte mit der rechten Maustaste auf die Microsoft Office 365 Identity Platform, und wählen Sie **Anspruchsregeln bearbeiten**. ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. Klicken Sie unter "Ausstellungstransformationsregeln" auf **Regel hinzufügen**. ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. Wählen Sie im Assistenten zum Hinzufügen von Transformationsanspruchsregeln im Dropdownmenü die Option "Passthrough oder eingehenden Anspruch filtern", und klicken Sie auf "Weiter". ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. Geben Sie der Regel im Feld neben „Anspruchsregelname“ einen Namen. Beispiel: InsideCorpNet.
7. Wählen Sie in der Dropdownliste neben "Eingehender Anspruchstyp" die Option "Innerhalb des Unternehmensnetzwerks". ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Klicken Sie auf Fertig stellen.
9. Klicken Sie unter "Ausstellungstransformationsregeln" auf **Regel hinzufügen**.
10. Wählen Sie im Assistenten zum Hinzufügen von Transformationsanspruchsregeln in der Dropdownliste die Option „Ansprüche mit benutzerdefinierter Regel senden“, und klicken Sie auf „Weiter“.
11. Geben Sie im Feld unter „Anspruchsregelname:“ den Text „Benutzeranmeldung aufrechterhalten“ ein.
12. Geben Sie in das Feld für benutzerdefinierte Regeln Folgendes ein:
	    
		c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
			=> issue(claim = c);
![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Klicken Sie auf **Fertig stellen**.
14. Klicken Sie auf **Anwenden**.
15. Klicken Sie auf **OK**.
16. Schließen Sie die AD FS-Verwaltung.



### Konfigurieren vertrauenswürdiger IPs der Azure Multi-Factor Authentication mit Partnerbenutzern
Da die Ansprüche jetzt vorhanden sind, können wir vertrauenswürdige IPs konfigurieren.

1. Melden Sie sich am Azure-Verwaltungsportal an.
2. Klicken Sie im linken Bereich auf "Active Directory".
3. Klicken Sie unter „Verzeichnis“ auf das Verzeichnis, für das Sie vertrauenswürdige IPs einrichten möchten.
4. Klicken Sie im ausgewählten Verzeichnis auf "Konfigurieren".
5. Klicken Sie im Abschnitt "Multi-Factor Authentication" auf "Diensteinstellungen verwalten".
6. Wählen Sie auf der Seite "Diensteinstellungen" unter "Vertrauenswürdige IPs" die Option **Für Anforderungen von Partnerbenutzern, die aus meinem Intranet stammen**. ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Klicken Sie auf "Speichern".
8. Sobald die Updates angewendet wurden, klicken Sie auf "Schließen".


Fertig! An diesem Punkt sollten Office 365-Partnerbenutzer nur MFA verwenden müssen, wenn ein Anspruch von außerhalb des Unternehmensintranets stammt.

<!---HONumber=August15_HO9-->