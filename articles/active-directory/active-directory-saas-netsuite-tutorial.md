<properties
   pageTitle="Tutorial: Integrieren von NetSuite in Azure Active Directory | Microsoft Azure"
   description="Erfahren Sie, wie Sie NetSuite mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und mehr zu aktivieren!"
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

#Tutorial: Integrieren von NetSuite in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Ihre NetSuite-Umgebung mit Ihrem Azure Active Directory \(Azure AD\) verbinden. Sie erfahren, wie Sie die einmalige Anmeldung bei NetSuite konfigurieren, die automatisierte Benutzerbereitstellung aktivieren und Benutzern den Zugriff auf NetSuite zuweisen.

##Voraussetzungen

1. Um über das [Azure-Verwaltungsportal](https://manage.windowsazure.com) auf Azure Active Directory zuzugreifen, müssen Sie über ein gültiges Azure-Abonnement verfügen.

2. Sie benötigen Administratorzugriff auf ein [NetSuite](http://www.netsuite.com/portal/home.shtml)-Abonnement. Sie können ein kostenloses Testkonto für einen der Dienste verwenden.

##Schritt 1: Hinzufügen von NetSuite zu Ihrem Verzeichnis

1. Klicken Sie im linken Navigationsbereich des [Azure-Verwaltungsportals](https://manage.windowsazure.com) auf **Active Directory**.

	![Wählen Sie im linken Navigationsbereich "Active Directory".][0]

2. Wählen Sie aus der Liste **Verzeichnis** das Verzeichnis, dem Sie NetSuite hinzufügen möchten.

3. Klicken Sie im oberen Menü auf **Anwendungen**.

	![Klicken Sie auf "Anwendungen".][1]

4. Klicken Sie unten auf der Seite auf **Hinzufügen**.

	![Klicken Sie auf "Hinzufügen", um eine neue Anwendung hinzufügen.][2]

5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

	![Klicken Sie auf "Anwendung aus dem Katalog hinzufügen".][3]

6. Geben Sie im **Suchfeld** den Wert **NetSuite** ein. Wählen Sie dann **NetSuite** aus den Ergebnissen, und klicken Sie auf **Fertig stellen**, um die Anwendung hinzuzufügen.

	![Fügen Sie NetSuite hinzu.][4]

7. Sie sollten jetzt die Seite "Schnellstart" für NetSuite sehen:

	![Seite "Schnellstart" für NetSuite in Azure AD][5]

##Schritt 2: Aktivieren der einmaligen Anmeldung

1. Klicken Sie in Azure AD auf der Seite "Schnellstart" für NetSuite auf die Schaltfläche **Einmaliges Anmelden konfigurieren**.

	![Schaltfläche "Einmaliges Anmelden konfigurieren"][6]

2. Ein Dialogfeld wird geöffnet, und Sie sehen einen Bildschirm mit der Frage: "Wie sollen sich Benutzer bei NetSuite anmelden?" Wählen Sie **Azure AD – einmaliges Anmelden**, und klicken Sie dann auf **Weiter**.

	![Wählen Sie "Azure AD – einmaliges Anmelden".][7]

	> [AZURE.NOTE]Um weitere Informationen zu den verschiedenen Optionen für die einmalige Anmeldung zu erhalten, [klicken Sie hier](https://msdn.microsoft.com/library/azure/dn308588.aspx).

3. Geben Sie auf der Seite **App-Einstellungen konfigurieren** für das Feld **Anmelde-URL** Ihre NetSuite-Mandanten-URL in einem der folgenden Formate ein:
	- `https://<tenant-name>.netsuite.com`
	- `https://<tenant-name>.na1.netsuite.com`
	- `https://<tenant-name>.na2.netsuite.com`
	- `https://<tenant-name>.sandbox.netsuite.com`
	- `https://<tenant-name>.na1.sandbox.netsuite.com`
	- `https://<tenant-name>.na2.sandbox.netsuite.com`

	![Geben Sie Ihre Mandanten-URL ein.][8]

4. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für NetSuite** auf **Metadaten herunterladen**, und speichern Sie die Zertifikatdatei lokal auf Ihrem Computer.

	![Laden Sie die Metadaten herunter.][9]

5. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich bei Ihrer Netsuite-Unternehmenswebsite als Administrator an.

6. Klicken Sie in der Symbolleiste am oberen Rand der Seite auf **Setup** und dann auf **Setup-Manager**.

	![Wechseln Sie zum Setup-Manager.][10]

7. Wählen Sie aus der Liste **Setupaufgaben** die Einstellung **Integration**.

	![Wechseln Sie zur Integration.][11]

8. Klicken Sie im Abschnitt **Authentifizierung verwalten** auf **Einmalige Anmeldung für SAML**.

	![Wechseln Sie zur einmaligen Anmeldung für SAML.][12]

9. Führen Sie auf der Seite **SAML-Setup** die folgenden Schritte aus:

	- Kopieren Sie in Azure Active Directory den Wert der **Remoteanmelde-URL**, und fügen Sie ihn in NetSuite in das Feld **Anmeldeseite für Identitätsanbieter** ein.

		![Die SAML-Setup-Seite.][13]

	- Wählen Sie in NetSuite **Primäre Authentifizierungsmethode**.

	- Wählen Sie für das Feld mit der Bezeichnung **SAMLV2-Identitätsanbietermetadaten** **IDP-Metadatendatei hochladen**. Klicken Sie dann zum Hochladen der Metadatendatei, die Sie in Schritt 4 heruntergeladen haben, auf **Durchsuchen**.

		![Laden Sie die Metadaten hoch.][16]

	- Klicken Sie auf **Senden**.

9. Aktivieren Sie in Azure AD das Kontrollkästchen zur Bestätigung der Konfiguration der einmaligen Anmeldung, um das Zertifikat zu aktivieren, das Sie nach NetSuite hochgeladen haben. Klicken Sie auf **Next**.

	![Aktivieren Sie das Kontrollkästchen zur Bestätigung.][14]

10. Geben Sie auf der letzten Seite des Dialogfelds eine E-Mail-Adresse ein, wenn Sie per E-Mail Fehler- und Warnmeldungen im Zusammenhang mit der Wartung dieser Konfiguration für die einmalige Anmeldung erhalten möchten.

	![Geben Sie Ihre E-Mail-Adresse ein.][15]

11. Klicken Sie auf **Fertig stellen**, um das Dialogfeld zu schließen. Klicken Sie anschließend am oberen Rand der Seite auf **Attribute**.

	![Klicken Sie auf Attribute.][17]

12. Klicken Sie auf **Benutzerattribut hinzufügen**.

	![Klicken Sie auf "Benutzerattribut hinzufügen".][18]

13. Geben Sie in das Feld **Attributname** `account` ein. Geben Sie in das Feld **Attributwert** Ihre Konto-ID für NetSuite ein. Beachten Sie die folgenden Anleitungen, um Ihre Konto-ID zu finden:

	![Fügen Sie Ihre Konto-ID von NetSuite hinzu.][19]

	- Klicken Sie in NetSuite im oberen Navigationsmenü auf **Setup**.
	- Klicken Sie dann im linken Navigationsmenü im Abschnitt **Setupaufgaben** auf den Abschnitt **Integration**, und klicken Sie auf **Webdiensteeinstellungen**.
	- Kopieren Sie Ihre Konto-ID für NetSuite, und fügen Sie sie in Azure AD in das Feld **Attributwert** ein.

		![Rufen Sie Ihre Konto-ID ab.][20]

14. Klicken Sie in Azure AD auf **Fertig stellen**, um das Hinzufügen des SAML-Attributs abzuschließen. Klicken Sie dann im unteren Menü auf **Änderungen übernehmen**.

	![Speichern Sie die Änderungen.][21]

15. Bevor Benutzer die einmalige Anmeldung in NetSuite ausführen können, müssen ihnen zunächst die entsprechenden Berechtigungen in NetSuite zugewiesen werden. Befolgen Sie die folgenden Anweisungen, um diese Berechtigungen zuzuweisen.

	- Klicken Sie im oberen Navigationsmenü auf **Setup** und dann auf **Setup-Manager**.

	![Wechseln Sie zum Setup-Manager.][10]

	- Wählen Sie im linken Navigationsmenü **Benutzer/Rollen**, und klicken Sie auf **Rollen verwalten**.

	![Wechseln Sie zum Verwalten von Rollen.][22]

	- Klicken Sie auf **Neue Rolle**.

	- Geben Sie einen **Namen** für die neue Rolle ein, und aktivieren Sie das Kontrollkästchen **Nur einmaliges Anmelden**.

	![Benennen Sie die neue Rolle.][23]

	- Klicken Sie auf **Speichern**.

	- Klicken Sie im oberen Menü auf **Berechtigungen**. Klicken Sie dann auf **Setup**.

	![Wechseln Sie zu Berechtigungen.][24]

	- Wählen Sie **Einrichten der einmaligen Anmeldung für SAM**, und klicken Sie dann auf **Hinzufügen**.

	- Klicken Sie auf **Speichern**.

	- Klicken Sie im oberen Navigationsmenü auf **Setup** und dann auf **Setup-Manager**.

	![Wechseln Sie zum Setup-Manager.][10]

	- Wählen Sie im linken Navigationsmenü **Benutzer/Rollen**, und klicken Sie auf **Benutzer verwalten**.

	![Wechseln Sie zum Verwalten von Benutzern.][25]

	- Wählen Sie einen Testbenutzer. Klicken Sie dann auf **Bearbeiten**.

	![Wechseln Sie zum Verwalten von Benutzern.][26]

	- Wählen Sie im Dialogfeld "Rollen" die Rolle, die Sie erstellt haben, und klicken Sie auf **Hinzufügen**.

	![Wechseln Sie zum Verwalten von Benutzern.][27]

	- Klicken Sie auf **Speichern**.

19. Weiter unten im Abschnitt [Zuweisen von Benutzern zu NetSuite](#step-4-assign-users-to-netsuite) erfahren Sie, wie Sie Ihre Konfiguration testen können.

##Schritt 3: Aktivieren der automatisierten Benutzerbereitstellung

1. Klicken Sie in Azure Active Directory auf der Seite "Schnellstart" für NetSuite auf **Benutzerbereitstellung konfigurieren**.

	![Konfigurieren der Benutzerbereitstellung][28]

2. Geben Sie in dem nun geöffneten Dialogfeld Ihre Administratoranmeldeinformationen für NetSuite ein, und klicken Sie auf **Weiter**.

	![Geben Sie Ihre NetSuite-Admin-Anmeldeinformationen ein.][29]

3. Geben Sie auf der Bestätigungsseite Ihre E-Mail-Adresse ein, um Benachrichtigungen über Fehler bei der Bereitstellung zu erhalten.

	![Bestätigen Sie den Vorgang.][30]

4. Klicken Sie auf **Fertig stellen**, um das Dialogfeld zu schließen.

##Schritt 4: Zuweisen von Benutzern zu NetSuite

1. Um Ihre Konfiguration zu testen, beginnen Sie mit dem Erstellen eines neuen Testkontos im Verzeichnis.

2. Klicken Sie auf der Seite "Schnellstart" für NetSuite auf die Schaltfläche **Benutzer zuweisen**.

	![Klicken Sie auf "Benutzer zuweisen".][31]

3. Wählen Sie Ihren Testbenutzer, und klicken Sie am unteren Bildschirmrand auf die Schaltfläche **Zuweisen**:

 - Wenn Sie die automatisierte Benutzerbereitstellung nicht aktiviert haben, werden Sie aufgefordert, Folgendes zu bestätigen:

		![Confirm the assignment.][32]

 - Wenn Sie die automatisierte Benutzerbereitstellung aktiviert haben, werden Sie aufgefordert, zu definieren, welche Art von Rolle der Benutzer in NetSuite erhalten soll. Neu bereitgestellte Benutzer sollten in Ihrer NetSuite-Umgebung nach einigen Minuten angezeigt werden.

4. Um Ihre Einstellungen für die einmalige Anmeldung zu testen, öffnen Sie den Zugriffsbereich unter [https://myapps.microsoft.com](https://myapps.microsoft.com/), melden Sie sich beim Testkonto an, und klicken Sie auf **NetSuite**.

##Siehe auch

- [Liste der Tutorials zur Integration von SaaS-Anwendungen](active-directory-saas-tutorial-list.md)
- [Anwendungszugriff in Azure AD](https://msdn.microsoft.com/library/azure/dn308590.aspx)
- [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/azure/dn308586.aspx)

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png

<!---HONumber=July15_HO5-->