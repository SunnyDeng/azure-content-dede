<properties 
	pageTitle="Erste Schritte mit Azure Multi-Factor Authentication in der Cloud" 
	description="Auf dieser Seite zur Azure Multi-Factor Authentication werden die ersten Schritte mit Azure MFA in der Cloud beschrieben." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>

# Erste Schritte mit Azure Multi-Factor Authentication in der Cloud



<center>![MFA in der Cloud](./media/multi-factor-authentication-get-started-cloud/cloud2.png)</center>

Nachdem Sie sich nun für die Multi-Factor Authentication in der Cloud entschieden haben, können Sie mit der Einrichtung fortfahren. Für die ersten Schritte mit der Azure Multi-Factor Authentication sind nur wenige Klicks erforderlich. Wenn Sie Multi-Factor Authentication für Office 365 oder Multi-Factor Authentication für Azure-Administratoren verwenden, können Sie mit Schritt 3 fortfahren.


1. [Anmelden für ein Azure-Abonnement](https://azure.microsoft.com/pricing/free-trial/)
	- Falls Sie noch nicht über ein Azure-Abonnement verfügen, müssen Sie sich für ein Abonnement registrieren. Wenn Sie die Verwendung von Azure MFA testen möchten, können Sie ein Testabonnement verwenden.
2. [Erstellen eines Multi-Factor Authentication-Anbieters](#creating-an-azure-multi-factor-auth-provider) oder [Zuweisen von Lizenzen zu Benutzern](#assigning-an-azure-ad-premium-or-enterprise-mobility-license-to-users)
	- Als Nächstes müssen Sie einen der folgenden Schritte ausführen. Erstellen Sie einen Azure Multi-Factor Authentication-Anbieter, und weisen Sie ihn Ihrem Verzeichnis zu, oder weisen Sie Ihren Benutzern Lizenzen zu. Lizenzen sind für Benutzer verfügbar, die über Azure MFA, Azure AD Premium bzw. EMS verfügen. Azure Multi-Factor Authentication ist im Lieferumfang von Azure Active Directory Premium enthalten. Die Anwendung ist auch in der Enterprise Mobility Suite enthalten. Falls Sie über ausreichend Lizenzen verfügen und alle Benutzer Multi-Factor Authentication verwenden können, müssen Sie keinen Anbieter für die Authentifizierung erstellen. 
3. [Aktivieren von Multi-Factor Authentication für die Benutzer](#turn-on-multi-factor-authentication-for-users)
	- Aktivieren Sie MFA für die Benutzer über das Office 365- oder das Azure-Portal. 
4. [Senden einer E-Mail an Endbenutzer mit einer Benachrichtigung über MFA](#send-email-to-end-users)
	- Sobald Multi-Factor Authentication für das Konto eines Benutzers aktiviert wurde, sollte Sie ihn mit einer entsprechenden Benachrichtigung per E-Mail darüber informieren. Der Benutzer wird beim nächsten Anmelden aufgefordert, den Vorgang abzuschließen, und mit der E-Mail erhält er diese Info. 



## Erstellen eines Anbieters für die mehrstufige Authentifizierung in Azure
Multi-Factor Authentication ist standardmäßig für globale Administratoren, die mit Azure Active Directory arbeiten, und für Office 365-Benutzer verfügbar. Falls Sie die erweiterten Funktionen nutzen möchten, müssen Sie die Vollversion von Azure MFA erwerben.

Ein Azure Multi-Factor Authentication-Anbieter wird verwendet, um die Features zu nutzen, die mit der Vollversion von Azure MFA bereitgestellt werden. Er wird für Benutzer verwendet, die keine Lizenzen über Azure MFA, Azure AD Premium oder EMS besitzen. Azure MFA, Azure AD Premium und EMS enthalten standardmäßig die Vollversion von Azure MFA. Wenn Sie im Besitz von Lizenzen sind, benötigen Sie keinen Azure Multi-Factor Authentication-Anbieter. In den folgenden Schritten wird veranschaulicht, wie Sie einen Azure Multi-Factor Authentication-Anbieter erstellen.

### So erstellen Sie einen Anbieter für mehrstufige Authentifizierung
--------------------------------------------------------------------------------

1. Melden Sie sich beim Azure-Portal als Administrator an.
2. Wählen Sie im linken Bereich "Active Directory" aus.
3. Wählen Sie auf der Seite "Active Directory" oben "Anbieter für mehrstufige Authentifizierung" aus. Klicken Sie dann unten auf **Neu**.
4. Wählen Sie unter „App Services“ den Eintrag „Multi-Factor Authentication-Anbieter“ aus, und wählen Sie dann „Schnellerfassung“.
5. Füllen Sie die folgenden Felder aus, und wählen Sie "Erstellen" aus.
	1. Name: Der Name des Multi-Factor Authentication-Anbieters.
	2. Nutzungsmodell: Das Nutzungsmodell des Anbieters für mehrstufige Authentifizierung.
		- Pro Authentifizierung: Ein Kaufmodell, bei dem die Kosten pro Authentifizierung berechnet werden. Wird in der Regel für Szenarien verwendet, in denen Azure Multi-Factor Authentication in einer kundenorientierten Anwendung verwendet wird.
		- Pro aktiviertem Benutzer: Ein Kaufmodell, bei dem die Kosten pro aktiviertem Benutzer berechnet werden. Wird in der Regel für den Mitarbeiterzugriff auf Anwendungen wie Office 365 verwendet.
	2. Verzeichnis: Der Azure Active Directory-Mandant, dem der Anbieter für mehrstufige Authentifizierung zugeordnet ist. Bedenken Sie dabei Folgendes:
		- Zum Erstellen eines Anbieters für mehrstufige Authentifizierung benötigen Sie kein Azure AD-Verzeichnis. Dieses kann leer gelassen werden, wenn Sie nur den Azure Multi-Factor Authentication-Server oder das Azure Multi-Factor Authentication-SDK verwenden möchten.
		- Sie müssen den Multi-Factor Authentication-Anbieter einem Azure AD-Verzeichnis zuordnen, um die erweiterten Features nutzen zu können.
		- Azure AD Connect, AAD Sync oder DirSync ist nur erforderlich, wenn Sie Ihre lokale Active Directory-Umgebung mit einem Azure AD-Verzeichnis synchronisieren. Wenn Sie nur ein Azure AD-Verzeichnis verwenden, das nicht synchronisiert ist, ist keine Synchronisierung erforderlich.
		



5. Nach dem Klicken auf „Erstellen“ wird der Multi-Factor Authentication-Anbieter erstellt, und es wird die folgende Meldung angezeigt: „Der Anbieter für Multi-Factor Authentication wurde erstellt.“ Klicken Sie auf "OK".

![MFA-Anbieter erstellen](./media/multi-factor-authentication-get-started-cloud/provider.png)
	 
## Zuweisen einer Azure MFA-, Azure AD Premium- oder Enterprise Mobility Suite-Lizenz zu Benutzern

Wenn Sie über Azure MFA, Azure AD Premium oder die Enterprise Mobility Suite verfügen, müssen Sie keinen Multi-Factor Auth-Anbieter erstellen. Sie müssen lediglich die Lizenzen Ihren Benutzern zuweisen und dann können Sie sie für MFA aktivieren.

### So weisen Sie eine Azure MFA-, Azure AD Premium- oder Enterprise Mobility Suite-Lizenz zu
--------------------------------------------------------------------------------

1. Melden Sie sich beim Azure-Portal als Administrator an.
2. Wählen Sie im linken Bereich **Active Directory** aus.
3. Doppelklicken Sie auf der Seite "Active Directory" auf das Verzeichnis mit den Benutzern, die Sie aktivieren möchten.
4. Wählen Sie oben auf der Seite des Verzeichnisses die Option **Lizenzen** aus.
5. Wählen Sie auf der Seite „Lizenzen“ die Option „Multi-Factor Authentication“, „Active Directory Premium“ oder „Enterprise Mobility Suite“ aus, und klicken Sie dann auf „Zuweisen“. ![Lizenzen zuweisen](./media/multi-factor-authentication-get-started-cloud/license2.png)
6. Wählen Sie im Dialogfeld die Benutzer oder Gruppen aus, denen Sie Lizenzen zuweisen möchten, und klicken Sie dann auf das Häkchen, um die Änderungen zu speichern.






## Aktivieren von Multi-Factor Authentication für Benutzer

Benutzerkonten in Azure Multi-Factor Authentication können die folgenden drei Zustände aufweisen:

Zustand | Beschreibung |Nicht-Browser-Apps betroffen| Hinweise 
:-------------: | :-------------: |:-------------: |:-------------: |
Deaktiviert | Der Standardzustand für einen neuen Benutzer, der nicht für Multi-Factor Authentication registriert ist.|Nein|Der Benutzer verwendet keine Multi-Factor Authentication.
Aktiviert |Der Benutzer wurde für Multi-Factor Authentication registriert.|Nein. Sie werden weiterhin ausgeführt, bis die Registrierung abgeschlossen ist.|Der Benutzer ist aktiviert, seine Registrierung ist jedoch nicht abgeschlossen. Er wird beim nächsten Anmelden aufgefordert, den Vorgang abzuschließen.
Erzwungen|Der Benutzer wurde registriert und hat die Registrierung für die Verwendung von Multi-Factor Authentication abgeschlossen.|Ja. Sie funktionieren erst, wenn App-Kennwörter erstellt und verwendet werden. | Der Benutzer hat die Registrierung abgeschlossen oder nicht abgeschlossen. Wenn er die Registrierung abgeschlossen hat, verwendet er anschließend Multi-Factor Authentication. Andernfalls wird der Benutzer beim nächsten Anmelden aufgefordert, den Vorgang abzuschließen.

Gehen Sie wie unten angegeben vor, um MFA für die Benutzer zu aktivieren.

### So aktivieren Sie Multi-Factor Authentication
--------------------------------------------------------------------------------
1.  Melden Sie sich beim Azure-Verwaltungsportal als Administrator an.
2.  Klicken Sie im linken Bereich auf "Active Directory".
3.  Klicken Sie unter "Verzeichnis" auf das Verzeichnis für den Benutzer, dem Sie die Erlaubnis erteilen möchten.
4.  Klicken Sie oben auf "Benutzer".
5.  Klicken Sie unten auf der Seite auf "Verwalten der Multi-Factor Authentication".
6.  Suchen Sie den Benutzer, für den Sie Multi-Factor Authentication aktivieren möchten. Sie müssen möglicherweise oben die Ansicht ändern. Stellen Sie sicher, dass der Status „Deaktiviert“ lautet, und aktivieren Sie das Kontrollkästchen neben seinem Namen.
7.  Klicken Sie auf der rechten Seite auf „Aktivieren“. Klicken Sie auf "Multi-Factor Authentication aktivieren".
8.  Es wird empfohlen, dass Sie den Benutzer benachrichtigen, nachdem Sie ihn aktiviert haben. Hierfür können Sie eine E-Mail verwenden. Diese sollte auch eine Information dazu enthalten, wie Benutzer ihre Nicht-Browser-Apps verwenden können, um eine Sperrung zu vermeiden.

<center>![Benutzer aktivieren](./media/multi-factor-authentication-get-started-cloud/user.png)</center>

Um den Status mit Windows PowerShell zu ändern, können Sie wie folgt vorgehen: Sie können `$st.State` in einen der erwähnten Status ändern.

		$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
		$st.RelyingParty = "*"
		$st.State = “Enabled”
		$sta = @($st)
		Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta


## Senden einer E-Mail an Endbenutzer

Nachdem Sie die Benutzer aktiviert haben, sollten Sie ihnen eine E-Mail senden, um sie zu informieren, dass sie ihre Kontaktinformationen angeben müssen. Unten ist eine E-Mail-Vorlage angegeben, die Sie verwenden können. Sie enthält einen Link zu einem Video, das sich die Benutzer ansehen können.

		Subject: ACTION REQUIRED: Your password for Outlook and other apps needs updated

		Body:

		For added security, we have enabled multi-factor authentication for your account. 

		Action Required: You will need to complete the enrollment steps below to make your account secure with multi-factor authentication.  

		What to expect once MFA is enabled:

		Multi-factor authentication requires a password that you know and a phone that you have in order to sign into browser applications and to access Office 365, Azure portals.

		For Office 365 non-browser applications such as outlook, lync, a mail client on your mobile device etc, a special password called an app password is required instead of your account password to sign in. App passwords are different than your account password, and are generated during the multi-factor authentication set up process. 

		Please follow these enrollment steps to avoid interruption of your Office 365 service:

			1.  Sign in to the Office 365 Portal at http://portal.microsoftonline.com.
			2.  Follow the instructions to set up your preferred multi-factor authentication method when signing into Office 365 using a web browser. 
			3.  Create one app password for each device.
			4.  Enter the same app password in all applicable apps on that device e.g. Outlook, Mail client, Lync, Word, Powerpoint, Excel, CRM etc. 
			5.  Update your Office client applications or other mobile applications to use an app password.

		You can visit http://aka.ms/mfasetup to create app passwords or change your MFA Setting.  Please bookmark this.

		NOTE: Before entering an app password, you will need to clear the sign-in information (delete sign-in info), restart the application,   and sign-in with the username and app password. Follow the steps documented : http://technet.microsoft.com/library/dn270518.aspx#apppassword.


		Watch a video showing these steps at http://g.microsoftonline.com/1AX00en/175.

		Best Regards,
		Your Administrator

## Nächste Schritte
Nachdem Sie die Multi-Factor Authentication in der Cloud eingerichtet haben, können Sie die Bereitstellung konfigurieren und einrichten. Weitere Informationen finden Sie unter [Konfigurieren von Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md).

<!---HONumber=AcomDC_0323_2016-->