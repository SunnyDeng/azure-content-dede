<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit Google Apps | Microsoft Azure"
    description="Erfahren Sie, wie Sie Google Apps mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und mehr zu aktivieren!"
    services="active-directory"
    documentationCenter=""
    authors="liviodlc"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/17/2016"
    ms.author="liviodlc"/>

#Tutorial: Integrieren von Google Apps in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Ihre Google Apps-Umgebung mit Ihrem Azure Active Directory (Azure AD) verbinden. Sie lernen, wie Sie die einmalige Anmeldung bei Google Apps konfigurieren, die automatisierte Benutzerbereitstellung aktivieren und Benutzern den Zugriff auf Google Apps zuweisen.

##Voraussetzungen

1. Um über das [klassische Azure-Portal](https://manage.windowsazure.com) auf Azure Active Directory zuzugreifen, müssen Sie über ein gültiges Azure-Abonnement verfügen.

2. Sie benötigen einen gültigen Mandanten für [Google Apps for Work](https://www.google.com/work/apps/) oder [Google Apps for Education](https://www.google.com/edu/products/productivity-tools/). Sie können ein kostenloses Testkonto für einen der Dienste verwenden.

##Videotutorial

So aktivieren Sie das einmalige Anmelden bei Google Apps in zwei Minuten

> [AZURE.VIDEO enable-single-sign-on-to-google-apps-in-2-minutes-with-azure-ad]

##Häufig gestellte Fragen

1. **F: Sind Chromebooks und andere Chrome-Geräte mit dem einmaligen Anmelden in Azure AD kompatibel?**

	A: Ja, Benutzer können sich mit ihren Azure AD-Anmeldeinformationen bei ihren Chromebook-Geräten anmelden. In diesem [Google Apps-Supportartikel](https://support.google.com/chrome/a/answer/6060880) finden Sie Informationen darüber, warum Benutzer möglicherweise zweimal zum Eingeben der Anmeldeinformationen aufgefordert werden.

2. **F: Wenn ich einmaliges Anmelden aktiviere, können Benutzer dann ihre Azure AD-Anmeldeinformationen zum Anmelden bei Google-Produkten wie Google Classroom, GMail, Google Drive, YouTube usw. verwenden?**

	A: Ja, je nachdem, [welche Google-Apps](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) Sie für Ihre Organisation aktivieren oder deaktivieren.

3. **F: Kann ich einmaliges Anmelden nur für einen Teil meiner Google Apps-Benutzer aktivieren?**

	A: Nein. Wenn Sie einmaliges Anmelden aktivieren, müssen sich all Ihre Google Apps-Benutzer sofort mit ihren Azure AD-Anmeldeinformationen authentifizieren. Da Google Apps die Verwendung mehrerer Identitätsanbieter nicht unterstützt, kann entweder Azure AD oder Google als Identitätsanbieter für Ihre Google Apps-Umgebung dienen – aber nicht beide gleichzeitig.

4. **F: Wenn ein Benutzer über Windows angemeldet ist, wird er dann automatisch bei Google Apps authentifiziert, ohne dass er zur Eingabe eines Kennworts aufgefordert wird?**

	A: Es gibt zwei Optionen zum Aktivieren dieses Szenarios. Benutzer können sich über die [Azure Active Directory-Einbindung](active-directory-azureadjoin-overview.md) bei Windows 10-Geräten anmelden. Alternativ dazu können Benutzer sich bei Windows-Geräten anmelden, die über eine Domäne mit einem lokalen Active Directory verbunden sind, das für einmaliges Anmelden bei Azure AD über eine [AD FS](active-directory-aadconnect-user-signin.md)-Bereitstellung (Active Directory-Verbunddienste) aktiviert wurde. Für beide Optionen müssen Sie das Tutorial unten befolgen, um einmaliges Anmelden zwischen Azure AD und Google Apps zu aktivieren.

##Schritt 1: Hinzufügen von Google Apps zu Ihrem Verzeichnis

1. Klicken Sie im linken Navigationsbereich des [klassischen Azure-Portals](https://manage.windowsazure.com) auf **Active Directory**.

	![Wählen Sie im linken Navigationsbereich "Active Directory".][0]

2. Wählen Sie aus der Liste **Verzeichnis** das Verzeichnis, dem Sie Google Apps hinzufügen möchten.

3. Klicken Sie im oberen Menü auf **Anwendungen**.

	![Klicken Sie auf "Anwendungen".][1]

4. Klicken Sie unten auf der Seite auf **Hinzufügen**.

	![Klicken Sie auf "Hinzufügen", um eine neue Anwendung hinzufügen.][2]

5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

	![Klicken Sie auf "Anwendung aus dem Katalog hinzufügen".][3]

6. Geben Sie im **Suchfeld** **Google Apps** ein. Wählen Sie dann **Google Apps** aus den Ergebnissen, und klicken Sie auf **Fertig stellen**, um die Anwendung hinzufügen.

	![Fügen Sie Google Apps hinzu.][4]

7. Sie sollten jetzt die Seite "Schnellstart" für Google Apps sehen:

	![Seite "Schnellstart" für Google Apps in Azure AD][5]

##Schritt 2: Aktivieren der einmaligen Anmeldung

1. Klicken Sie in Azure AD auf der Seite "Schnellstart" für Google Apps auf die Schaltfläche **Einmaliges Anmelden konfigurieren**.

	![Schaltfläche zum Konfigurieren der einmaligen Anmeldung][6]

2. Ein Dialogfeld wird geöffnet, und Sie sehen einen Bildschirm mit der Frage: "Wie sollen sich Benutzer bei Google Apps anmelden?" Wählen Sie **Azure AD – einmaliges Anmelden**, und klicken Sie dann auf **Weiter**.

	![Wählen Sie "Azure AD – einmaliges Anmelden".][7]

	> [AZURE.NOTE] Um weitere Informationen zu den verschiedenen Optionen für die einmalige Anmeldung zu erhalten, [klicken Sie hier](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work).

3. Geben Sie auf der Seite **App-Einstellungen konfigurieren** für das Feld **Anmelde-URL** Ihre Google Apps-Mandanten-URL im folgenden Format ein: `https://mail.google.com/a/<yourdomain>`

	![Geben Sie Ihre Mandanten-URL ein.][8]

4. Geben Sie auf der Seite **Einmaliges Anmelden automatisch konfigurieren** die Domäne für Ihren Google Apps-Mandanten ein. Klicken Sie dann auf die Schaltfläche **Konfigurieren**.

	![Geben Sie Ihren Domänennamen ein, und klicken Sie auf „Konfigurieren“.](./media/active-directory-saas-google-apps-tutorial/ga-auto-config.png)

	> [AZURE.NOTE] Wenn Sie das einmalige Anmelden lieber manuell konfigurieren möchten, finden Sie entsprechende Informationen unter [Optionaler Schritt: Manuelles Konfigurieren des einmaligen Anmeldens](#optional-step-manually-configure-single-sign-on)

5. Melden Sie sich mit Ihrem Google Apps-Administratorkonto an. Klicken Sie dann auf **Zulassen**, um zu erlauben, dass Azure Active Directory Konfigurationsänderungen an Ihrem Google Apps-Abonnement vornimmt.

	![Geben Sie Ihren Domänennamen ein, und klicken Sie auf „Konfigurieren“.](./media/active-directory-saas-google-apps-tutorial/ga-consent.PNG)

6. Warten Sie einige Sekunden, bis Azure Active Directory Ihren Google Apps-Mandanten konfiguriert hat. Klicken Sie nach Abschluss dieses Vorgangs auf **Weiter**.

10. Geben Sie auf der letzten Seite des Dialogfelds eine E-Mail-Adresse ein, wenn Sie per E-Mail Fehler- und Warnmeldungen im Zusammenhang mit der Wartung dieser Konfiguration für die einmalige Anmeldung erhalten möchten.

	![Geben Sie Ihre E-Mail-Adresse ein.][14]

11. Klicken Sie auf **Fertig stellen**, um das Dialogfeld zu schließen. Weiter unten im Abschnitt [Zuweisen von Benutzern zu Google Apps](#step-4-assign-users-to-google-apps) erfahren Sie, wie Sie Ihre Konfiguration testen können.

##Optionaler Schritt: Manuelles Konfigurieren des einmaligen Anmeldens

Wenn Sie das einmalige Anmelden lieber manuell einrichten möchten, führen Sie die folgenden Schritte aus:

1. Klicken Sie in Azure AD auf der Seite "Schnellstart" für Google Apps auf die Schaltfläche **Einmaliges Anmelden konfigurieren**.

	![Schaltfläche "Einmaliges Anmelden konfigurieren"][6]

2. Ein Dialogfeld wird geöffnet, und Sie sehen einen Bildschirm mit der Frage: "Wie sollen sich Benutzer bei Google Apps anmelden?" Wählen Sie **Azure AD – einmaliges Anmelden**, und klicken Sie dann auf **Weiter**.

	![Wählen Sie "Azure AD – einmaliges Anmelden".][7]

	> [AZURE.NOTE] Um weitere Informationen zu den verschiedenen Optionen für die einmalige Anmeldung zu erhalten, [klicken Sie hier](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work).

3. Geben Sie auf der Seite **App-Einstellungen konfigurieren** für das Feld **Anmelde-URL** Ihre Google Apps-Mandanten-URL im folgenden Format ein: `https://mail.google.com/a/<yourdomain>`

	![Geben Sie Ihre Mandanten-URL ein.][8]

4. Aktivieren Sie auf der Seite **Einmaliges Anmelden automatisch konfigurieren** das Kontrollkästchen **Diese Anwendung manuell für einmaliges Anmelden konfigurieren**. Klicken Sie auf **Next**.

	![Wählen Sie die manuelle Konfiguration aus.](./media/active-directory-saas-google-apps-tutorial/ga-auto-skip.PNG)

4. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Google Apps** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.

	![Laden Sie das Zertifikat herunter.][9]

5. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich bei der [Google Apps-Verwaltungskonsole](http://admin.google.com/) mit Ihrem Administratorkonto an.

6. Klicken Sie auf **Sicherheit**. Wenn der Link nicht angezeigt wird, kann er unter dem Menü **Weitere Steuerelemente** im unteren Bereich des Bildschirms versteckt sein.

	![Klicken Sie auf "Sicherheit".][10]

7. Klicken Sie auf der Seite **Sicherheit** auf **Einmaliges Anmelden (SSO) einrichten**.

	![Klicken Sie auf "SSO".][11]

8. Führen Sie die folgenden Konfigurationsänderungen durch:

	![Konfigurieren von SSO][12]

	- Wählen Sie **Einmaliges Anmelden mit externem Identitätsanbieter einrichten**.

	- Kopieren Sie in Azure AD die **Dienst-URL für einmalige Anmeldung**, und fügen Sie sie in Google Apps in das Feld **URL der Anmeldeseite** ein.

	- Kopieren Sie in Azure AD die **Dienst-URL für einmalige Abmeldung**, und fügen Sie sie in Google Apps in das Feld **URL der Abmeldeseite** ein.

	- Kopieren Sie in Azure AD die **Kennwortänderungs-URL**, und fügen Sie sie in Google Apps in das Feld **Kennwortänderungs-URL** ein.

	- Laden Sie in Google Apps als **Verifizierungszertifikat** das Zertifikat hoch, das Sie in Schritt 4 heruntergeladen haben.

	- Klicken Sie auf **Änderungen speichern**.

9. Aktivieren Sie in Azure AD das Kontrollkästchen zur Bestätigung der Konfiguration der einmaligen Anmeldung, um das in Google Apps hochgeladene Zertifikat zu aktivieren. Klicken Sie auf **Weiter**.

	![Aktivieren Sie das Kontrollkästchen zur Bestätigung.][13]

10. Geben Sie auf der letzten Seite des Dialogfelds eine E-Mail-Adresse ein, wenn Sie per E-Mail Fehler- und Warnmeldungen im Zusammenhang mit der Wartung dieser Konfiguration für die einmalige Anmeldung erhalten möchten.

	![Geben Sie Ihre E-Mail-Adresse ein.][14]

11. Klicken Sie auf **Fertig stellen**, um das Dialogfeld zu schließen. Weiter unten im Abschnitt [Zuweisen von Benutzern zu Google Apps](#step-4-assign-users-to-google-apps) erfahren Sie, wie Sie Ihre Konfiguration testen können.

##Schritt 3: Aktivieren der automatisierten Benutzerbereitstellung

> [AZURE.NOTE] Eine weitere Option für die Automatisierung der Benutzerbereitstellung für Google Apps ist die Verwendung von [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en), um Ihre lokalen Active Directory-Identitäten für Google Apps bereitzustellen. Im Gegensatz dazu stellt die Lösung in diesem Tutorial Ihre Azure Active Directory-Benutzer (Cloud) und E-Mail-aktivierten Gruppen für Google Apps bereit.

1. Melden Sie sich bei der[Google Apps-Verwaltungskonsole](http://admin.google.com/) mit Ihrem Administratorkonto an, und klicken Sie auf **Sicherheit**. Wenn der Link nicht angezeigt wird, kann er unter dem Menü **Weitere Steuerelemente** im unteren Bereich des Bildschirms versteckt sein.

	![Klicken Sie auf "Sicherheit".][10]

2. Klicken Sie auf der Seite **Sicherheit** auf **API-Referenz**.

	![Klicken Sie auf "API-Referenz".][15]

3. Wählen Sie **API-Zugriff aktivieren** aus.

	![Klicken Sie auf "API-Referenz".][16]

	> [AZURE.IMPORTANT] Für jeden Benutzer, den Sie für Google Apps bereitstellen möchten, *muss* der Benutzername in Azure Active Directory an eine benutzerdefinierte Domäne gebunden werden. Benutzernamen, die z. B. wie bob@contoso.onmicrosoft.com aussehen, werden von Google Apps nicht akzeptiert, während bob@contoso.com akzeptiert wird. Sie können die Domäne eines vorhandenen Benutzers ändern, indem Sie seine Eigenschaften in Azure AD bearbeiten. Anweisungen zum Festlegen einer benutzerdefinierten Domäne für Azure Active Directory und Google Apps sind unten aufgeführt.

4. Wenn Sie Ihrem Azure Active Directory noch keinen benutzerdefinierten Domänennamen hinzugefügt haben, führen Sie die folgenden Schritte aus:

	- Klicken Sie im linken Navigationsbereich des [klassischen Azure-Portals](https://manage.windowsazure.com) auf **Active Directory**. Wählen Sie in der Verzeichnisliste Ihr Verzeichnis aus. 

	- Klicken Sie im Menü der obersten Ebene auf **Domänen**, und klicken Sie dann auf **Benutzerdefinierte Domäne hinzufügen**.

		![Hinzufügen einer benutzerdefinierten Domäne][17]

	- Geben Sie Ihren Domänennamen in das Feld **Domänenname** ein. Dies sollte der gleiche Domänenname sein, den Sie für Google Apps verwenden möchten. Wenn Sie fertig sind, klicken Sie auf die Schaltfläche **Hinzufügen**.

		![Geben Sie Ihren Domänennamen ein.][18]

	- Klicken Sie auf **Weiter**, um zur Überprüfungsseite zu wechseln. Um sicherzustellen, dass Sie diese Domäne besitzen, müssen Sie die DNS-Einträge der Domäne gemäß der Werte bearbeiten, die auf dieser Seite bereitstehen. Sie können auch wählen, ob Sie zur Überprüfung **MX-Einträge** oder **TXT-Einträge** verwenden möchten; dies hängt von Ihrer Auswahl für die Option **Datensatztyp** ab. Ausführlichere Anweisungen zum Überprüfen von Domänennamen mit Azure AD finden Sie unter [Hinzufügen eigener Domänennamen zu Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).

		![Überprüfen Sie Ihren Domänennamen.][19]

	- Wiederholen Sie die obigen Schritte für alle Domänen, die Sie Ihrem Verzeichnis hinzufügen möchten.

5. Da Sie jetzt alle Ihre Domänen mit Azure AD überprüft haben, müssen Sie sie erneut mit Google Apps überprüfen. Führen Sie für jede Domäne, die noch nicht für Google Apps registriert ist, die folgenden Schritte aus:

	- Klicken Sie in der [Google Apps-Verwaltungskonsole](http://admin.google.com/) auf **Domänen**.

		![Klicken Sie auf "Domänen"][20]

	- Klicken Sie auf **Domäne oder Domänenalias hinzufügen**.

		![Hinzufügen einer neuen Domäne][21]

	- Wählen Sie **Andere Domäne hinzufügen**, und geben Sie den Namen der Domäne ein, die Sie hinzufügen möchten.

		![Geben Sie Ihren Domänennamen ein][22]

	- Klicken Sie auf **Weiter und Domänenbesitzrecht überprüfen**. Führen Sie dann die Schritte aus, mit denen Sie prüfen, ob Sie den Domänennamen besitzen. Umfassende Anweisungen zum Überprüfen Ihrer Domäne mit Google Apps finden Sie unter [Überprüfen des Websitebesitzrechts mit Google Apps](https://support.google.com/webmasters/answer/35179).

	- Wiederholen Sie die obigen Schritte für alle weiteren Domänen, die Sie zu Google Apps hinzufügen möchten.

	> [AZURE.WARNING] Wenn Sie die primäre Domäne für Ihren Google Apps-Mandanten ändern und bereits einmaliges Anmelden mit Azure AD konfiguriert haben, müssen Sie Schritt 3 unter [Schritt 2: Aktivieren der einmaligen Anmeldung](#step-two-enable-single-sign-on) wiederholen.

6. Klicken Sie in der [Google Apps-Verwaltungskonsole](http://admin.google.com/) auf **Administratorrollen**.

	![Klicken Sie auf "Google Apps".][26]

7. Ermitteln Sie, welches Administratorkonto Sie zum Verwalten der Benutzerbereitstellung verwenden möchten. Bearbeiten Sie für die **Administratorrolle** des Kontos die **Berechtigungen** für diese Rolle. Stellen Sie sicher, dass alle **Admin-API-Berechtigungen** aktiviert sind, sodass dieses Konto für die Bereitstellung verwendet werden kann.

	![Klicken Sie auf "Google Apps".][27]

	> [AZURE.NOTE] Wenn Sie eine Produktionsumgebung konfigurieren, sollten Sie speziell für diesen Schritt ein neues Administratorkonto in Google Apps erstellen. Diesem Konto muss eine Administratorrolle zugeordnet sein, die über die erforderlichen API-Berechtigungen verfügt.

8. Klicken Sie in Azure Active Directory im Menü der obersten Ebene auf **Anwendungen**, und klicken Sie dann auf**Google Apps**.

	![Klicken Sie auf "Google Apps".][23]

9. Klicken Sie auf der Seite "Schnellstart" für Google Apps auf **Benutzerbereitstellung konfigurieren**.

	![Konfigurieren der Benutzerbereitstellung][24]

10. Klicken Sie im daraufhin geöffneten Dialogfeld auf **Benutzerbereitstellung aktivieren**, um sich mit dem Google Apps-Administratorkonto zu authentifizieren, das Sie zum Verwalten der Bereitstellung verwenden möchten.

	![Aktivieren der Bereitstellung][25]

11. Bestätigen Sie, dass Sie Azure Active Directory die Berechtigung erteilen möchten, Änderungen an Ihrem Google Apps-Mandanten vorzunehmen.

	![Überprüfen Sie die Berechtigungen.][28]

12. Klicken Sie auf **Fertig stellen**, um das Dialogfeld zu schließen.

##Schritt 4: Zuweisen von Benutzern zu Google Apps

1. Um Ihre Konfiguration zu testen, beginnen Sie mit dem Erstellen eines neuen Testkontos im Verzeichnis.

2. Klicken Sie auf der Seite "Schnellstart" für Google Apps auf die Schaltfläche **Benutzer zuweisen**.

	![Klicken Sie auf "Benutzer zuweisen".][29]

3. Wählen Sie Ihren Testbenutzer aus, und klicken Sie am unteren Bildschirmrand auf die Schaltfläche **Zuweisen**:

 - Wenn Sie die automatisierte Benutzerbereitstellung nicht aktiviert haben, werden Sie aufgefordert, Folgendes zu bestätigen:

		![Confirm the assignment.][30]

 - Wenn Sie die automatisierte Benutzerbereitstellung aktiviert haben, werden Sie aufgefordert, zu definieren, welche Art von Rolle der Benutzer in Google Apps erhalten soll. Neu bereitgestellte Benutzer sollten in Ihrer Google Apps-Umgebung nach einigen Minuten angezeigt werden.

4. Um Ihre Einstellungen für einmaliges Anmelden zu testen, öffnen Sie den Zugriffsbereich unter [https://myapps.microsoft.com](https://myapps.microsoft.com/), melden Sie sich bei Ihrem Testkonto an, und klicken Sie auf **Google Apps**.

## Verwandte Artikel

- [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)
- [Liste der Tutorials zur Integration von SaaS-Apps](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-google-apps-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-google-apps-tutorial/add-app.png
[3]: ./media/active-directory-saas-google-apps-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-google-apps-tutorial/add-gapps.png
[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png

<!---HONumber=AcomDC_0218_2016-->