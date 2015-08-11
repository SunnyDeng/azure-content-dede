<properties
   pageTitle="Tutorial: Integrieren von Salesforce in Azure Active Directory | Microsoft Azure"
   description="Erfahren Sie, wie Sie Salesforce mit Azure Active Directory verwenden können, um einmalige Anmeldung, automatisierte Bereitstellung und mehr zu aktivieren!"
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="TerryLanfear"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/15/2015"
   ms.author="liviodlc"/>

#Tutorial: Integrieren von Salesforce in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Ihre Salesforce-Umgebung mit Ihrem Azure Active Directory verbinden. Sie erfahren, wie Sie die einmalige Anmeldung bei Salesforce konfigurieren, die automatisierte Benutzerbereitstellung aktivieren und Benutzern den Zugriff auf Salesforce zuweisen.

##Voraussetzungen

1. Um über das [Azure-Verwaltungsportal](https://manage.windowsazure.com) auf Azure Active Directory zuzugreifen, müssen Sie über ein gültiges Azure-Abonnement verfügen.

2. Sie müssen einen gültigen Mandanten in [Salesforce.com](https://www.salesforce.com/) besitzen.

> [AZURE.IMPORTANT]Bei Verwendung eines Salesforce.com-**Testkontos** können Sie die automatisierte Benutzerbereitstellung nicht konfigurieren. Bei Testkonten ist der erforderliche API-Zugriff erst nach dem Erwerb aktiviert.
> 
> Sie können diese Einschränkung umgehen, indem Sie ein [kostenloses Entwicklerkonto](https://developer.salesforce.com/signup) verwenden, um dieses Tutorial durchzuführen.

Wenn Sie eine Salesforce Sandbox-Umgebung verwenden, rufen Sie das [Tutorial: Azure Active Directory-Integration in Salesforce Sandbox](https://go.microsoft.com/fwLink/?LinkID=521879) auf.

##Videotutorials

Sie können zu diesem Tutorial die folgenden Videos nutzen.

**Videotutorial, Teil 1: Aktivieren der einmaligen Anmeldung**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**Videotutorial, Teil 2: Automatisieren der Benutzerbereitstellung**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##Schritt 1: Hinzufügen von Salesforce zu Ihrem Verzeichnis

1. Klicken Sie im linken Navigationsbereich des [Azure-Verwaltungsportals](https://manage.windowsazure.com) auf **Active Directory**.

	![Wählen Sie im linken Navigationsbereich "Active Directory".][0]

2. Wählen Sie aus der Liste **Verzeichnis** das Verzeichnis aus, dem Sie Salesforce hinzufügen möchten.

3. Klicken Sie im oberen Menü auf **Anwendungen**.

	![Klicken Sie auf "Anwendungen".][1]

4. Klicken Sie unten auf der Seite auf **Hinzufügen**.

	![Klicken Sie auf "Hinzufügen", um eine neue Anwendung hinzufügen.][2]

5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

	![Klicken Sie auf "Anwendung aus dem Katalog hinzufügen".][3]

6. Geben Sie in das **Suchfeld** **Salesforce** ein. Wählen Sie dann **Salesforce** aus den Ergebnissen, und klicken Sie auf **Fertig stellen**, um die Anwendung hinzufügen.

	![Fügen Sie Salesforce hinzu.][4]

7. Sie sollten jetzt die Seite "Schnellstart" für Salesforce sehen:

	![Seite "Schnellstart" für Salesforce in Azure AD][5]

##Schritt 2: Aktivieren der einmaligen Anmeldung

1. Bevor Sie die einmalige Anmeldung konfigurieren können, müssen Sie eine benutzerdefinierte Domäne für Ihre Salesforce-Umgebung einrichten und bereitstellen. Anleitungen hierzu finden Sie unter [Set Up a Domain Name](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US) (in englischer Sprache).

2. Klicken Sie auf der Seite "Schnellstart" von Salesforce in Azure AD auf die Schaltfläche **Einmaliges Anmelden konfigurieren**.

	![Schaltfläche zum Konfigurieren der einmaligen Anmeldung][6]

3. Ein Dialogfeld wird geöffnet, und Sie sehen einen Bildschirm mit der Frage: "Wie sollen sich Benutzer bei Salesforce anmelden?" Wählen Sie **Azure AD – einmaliges Anmelden**, und klicken Sie dann auf **Weiter**.

	![Wählen Sie "Azure AD – einmaliges Anmelden".][7]

	> [AZURE.NOTE]Um weitere Informationen zu den verschiedenen Optionen für die einmalige Anmeldung zu erhalten, [klicken Sie hier](https://msdn.microsoft.com/library/azure/dn308588.aspx).

4. Tragen Sie auf der Seite **App-Einstellungen konfigurieren** die **Anmelde-URL** durch Eingabe Ihrer Salesforce-Domänen-URL in folgendem Format ein:
 - Enterprise-Konto:`https://<domain>.my.salesforce.com`
 - Developer-Konto:`https://<domain>-dev-ed.my.salesforce.com` 

	![Geben Sie Ihre Anmelde-URL ein.][8]

5. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Salesforce** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.

	![Laden Sie das Zertifikat herunter.][9]

6. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich mit Ihrem Salesforce-Administratorkonto an.

7. Klicken Sie im Navigationsbereich unter **Administrator** auf **Sicherheitssteuerelemente**, um den entsprechenden Abschnitt zu erweitern. Klicken Sie dann auf **Einstellungen für einmaliges Anmelden**.

	![Klicken Sie unter "Sicherheitssteuerelemente" auf "Einstellungen für einmaliges Anmelden".][10]

8. Klicken Sie auf der Seite **Einstellungen für einmaliges Anmelden** auf die Schaltfläche **Bearbeiten**.

	![Klicken Sie auf die Schaltfläche "Bearbeiten"][11]

	> [AZURE.NOTE]Wenn Sie keine Einstellungen für die einmalige Anmeldung für Ihr Salesforce-Konto aktivieren können, müssen Sie sich an den Salesforces-Support wenden, damit das Feature für Sie aktiviert wird.

9. Wählen Sie **SAML aktiviert**, und klicken Sie dann auf **Speichern**.

	![Wählen Sie "SAML aktiviert".][12]

10. Um Ihre SAML-Einstellungen für die einmalige Anmeldung zu konfigurieren, klicken Sie auf **Neu**.

	![Wählen Sie "SAML aktiviert".][13]

11. Nehmen Sie auf der Seite **SAML-Einstellung für einmaliges Anmelden bearbeiten** folgende Konfigurationen vor:

	![Screenshot der Konfigurationen, die Sie durchführen sollten][14]

 - Geben Sie in das Feld **Name** einen Anzeigenamen für diese Konfiguration ein. Bei Eingabe eines Werts für **Name** wird automatisch das Textfeld **API-Name** gefüllt.

 - Kopieren Sie in Azure AD den Wert **Aussteller-URL**, und fügen Sie ihn dann in das Feld **Aussteller** in Salesforce ein.

 - Geben Sie in das **Entitäts-ID-Textfeld** Ihren Salesforce-Domänennamen nach folgendem Muster ein:
     - Enterprise-Konto:`https://<domain>.my.salesforce.com`
     - Developer-Konto:`https://<domain>-dev-ed.my.salesforce.com`

 - Klicken Sie auf **Durchsuchen** oder **Datei auswählen**, um das Dialogfeld **Datei zum Hochladen auswählen** zu öffnen. Wählen Sie Ihr Salesforce-Zertifikat, und klicken Sie dann zum Hochladen des Zertifikats auf **Öffnen**.

 - Wählen Sie für **SAML-Identitätstyp** **Erklärung enthält salesforce.com-Benutzernamen des Benutzers**.

 - Wählen Sie für **SAML-Identitätsstandort** **Identität ist das Namensbezeichnerelement der Ausstellererklärung**

 - Kopieren Sie in Azure AD den Wert der **Remoteanmelde-URL**, und fügen Sie ihn in Salesforce in das Feld **Anmelde-URL für Identitätsanbieter** ein.

 - Wählen Sie für **Vom Dienstanbieter initiierte Anforderungsbindung** die Einstellung **HTTP-Umleitung**.

 - Klicken Sie abschließend auf**Speichern**, um Ihre SAML-Einstellungen für die einmalige Anmeldung anzuwenden.

12. Klicken Sie in Salesforce im linken Navigationsbereich auf **Domänenverwaltung**, um den entsprechenden Abschnitt zu erweitern, und klicken Sie dann auf **Meine Domäne**.

	![Klicken Sie auf "Meine Domäne"][15]

13. Führen Sie einen Bildlauf nach unten zum Abschnitt **Authentifizierungskonfiguration** aus, und klicken Sie auf die Schaltfläche **Bearbeiten**.

	![Klicken Sie auf die Schaltfläche "Bearbeiten"][16]

14. Wählen Sie im Abschnitt **Authentifizierungsdienst** den Anzeigenamen der SAML-SSO-Konfiguration aus, und klicken Sie dann auf **Speichern**.

	![Wählen Sie Ihre SSO-Konfiguration][17]

	> [AZURE.NOTE]Wenn mehrere Authentifizierungsdienste aktiviert sind, und Benutzer versuchen, die einmalige Anmeldung für Ihre Salesforce-Umgebung zu initiieren, werden sie aufgefordert, einen Authentifizierungsdienst zur Anmeldung auszuwählen. Wenn Sie dies nicht möchten, sollten Sie **alle anderen Authentifizierungsdienste deaktiviert lassen**.

15. Aktivieren Sie in Azure AD das Kontrollkästchen zur Bestätigung der Konfiguration der einmaligen Anmeldung, um das Zertifikat zu aktivieren, das Sie nach Salesforce hochgeladen haben. Klicken Sie auf **Weiter**.

	![Aktivieren Sie das Kontrollkästchen zur Bestätigung.][18]

16. Geben Sie auf der letzten Seite des Dialogfelds eine E-Mail-Adresse ein, wenn Sie per E-Mail Fehler- und Warnmeldungen im Zusammenhang mit der Wartung dieser Konfiguration für die einmalige Anmeldung erhalten möchten.

	![Geben Sie Ihre E-Mail-Adresse ein.][19]

17. Klicken Sie auf **Fertig stellen**, um das Dialogfeld zu schließen. Weiter unten im Abschnitt [Zuweisen von Benutzern zu Salesforce](#step-4-assign-users-to-salesforce) erfahren Sie, wie Sie Ihre Konfiguration testen können.

##Schritt 3: Aktivieren der automatisierten Benutzerbereitstellung

1. Klicken Sie auf der Azure AD-Seite "Schnellstart" für Salesforce auf die Schaltfläche **Benutzerbereitstellung konfigurieren**.

	![Klicken Sie auf die Schaltfläche "Benutzerbereitstellung konfigurieren"][20]

2. Geben Sie im Dialogfeld **Benutzerbereitstellung konfigurieren** Ihren Administratorbenutzernamen und Ihr Kennwort für Salesforce ein.

	![Geben Sie Ihren Administratorbenutzernamen oder Ihr Kennwort ein.][21]

	> [AZURE.NOTE]Wenn Sie eine Produktionsumgebung konfigurieren, sollten Sie speziell für diesen Schritt ein neues Administratorkonto in Salesforce erstellen. Diesem Konto muss in Salesforce das Profil **Systemadministrator** zugewiesen werden.

3. Um Ihr Salesforce-Sicherheitstoken abzurufen, öffnen Sie eine neue Registerkarte, und melden Sie sich mit dem gleichen Salesforce-Administratorkonto an. Klicken Sie in der oberen rechten Ecke der Seite auf Ihren Namen und dann auf **Meine Einstellungen**.

	![Klicken Sie auf Ihren Namen und dann auf "Meine Einstellungen"][22]

4. Klicken Sie im linken Navigationsbereich auf **Persönlich**, um den entsprechenden Abschnitt zu erweitern, und dann auf **Mein Sicherheitstoken zurücksetzen**.

	![Klicken Sie auf Ihren Namen und dann auf "Meine Einstellungen"][23]

5. Klicken Sie auf der Seite **Mein Sicherheitstoken zurücksetzen** auf die Schaltfläche **Sicherheitstoken zurücksetzen**.

	![Lesen Sie die Warnungen.][24]

6. Überprüfen Sie den E-Mail-Posteingang dieses Administratorkontos. Achten Sie auf eine E-Mail von Salesforce.com, die das neue Sicherheitstoken enthält.

7. Kopieren Sie das Token, wechseln Sie zu Ihrem Azure AD-Fenster, und fügen Sie es in das Feld **Benutzersicherheitstoken** ein. Klicken Sie auf **Weiter**.

	![Fügen Sie das Sicherheitstoken ein.][25]

8. Auf der Bestätigungsseite können Sie wählen, E-Mail-Benachrichtigungen zu empfangen, wenn bei der Bereitstellung Fehler auftreten. Klicken Sie auf **Fertig stellen**, um das Dialogfeld zu schließen.

	![Geben Sie Ihre E-Mail-Adresse ein, um Benachrichtigungen zu erhalten.][26]

##Schritt 4: Zuweisen von Benutzern zu Salesforce

1. Um Ihre Konfiguration zu testen, beginnen Sie mit dem Erstellen eines neuen Testkontos im Verzeichnis.

2. Klicken Sie auf der Seite "Schnellstart" für Salesforce auf die Schaltfläche **Benutzer zuweisen**.

	![Klicken Sie auf "Benutzer zuweisen".][27]

3. Wählen Sie Ihren Testbenutzer, und klicken Sie am unteren Bildschirmrand auf die Schaltfläche **Zuweisen**:

 - Wenn Sie die automatisierte Benutzerbereitstellung nicht aktiviert haben, werden Sie aufgefordert, Folgendes zu bestätigen:

		![Confirm the assignment.][28]

 - Wenn Sie die automatisierte Benutzerbereitstellung aktiviert haben, werden Sie aufgefordert, zu definieren, welche Art von Salesforce-Profil der Benutzer erhalten soll. Neu bereitgestellte Benutzer sollten in Ihrer Salesforce-Umgebung nach einigen Minuten angezeigt werden.

		![Confirm the assignment.][29]

		> [AZURE.IMPORTANT] Bei der Bereitstellung in eine Salesforce-**Entwickler**umgebung steht Ihnen für jedes Profil eine sehr begrenzte Anzahl an Lizenzen zur Verfügung. Daher sollten Benutzer möglichst im **Chatter Free User**-Profil bereitgestellt werden, für das 4.999 Lizenzen verfügbar sind.

4. Um Ihre Einstellungen für die einmalige Anmeldung zu testen, öffnen Sie den Zugriffsbereich unter [https://myapps.microsoft.com](https://myapps.microsoft.com/), melden Sie sich beim Testkonto an, und klicken Sie auf **Salesforce**.

##Weitere Informationen

- [Liste der Tutorials zur Integration von SaaS-Anwendungen](active-directory-saas-tutorial-list.md)
- [Anwendungszugriff in Azure AD](https://msdn.microsoft.com/library/azure/dn308590.aspx)
- [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/azure/dn308586.aspx)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png

<!----HONumber=July15_HO5-->