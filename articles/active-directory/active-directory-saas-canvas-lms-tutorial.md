<properties pageTitle="Lernprogramm: Azure Active Directory-Integration mit Canvas LMS | Microsoft Azure" description="Erfahren Sie, wie Sie Canvas LMS mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Lernprogramm: Azure Active Directory-Integration mit Canvas LMS
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=524182), um Feedback abzugeben.

In diesem Lernprogramm wird die Integration von Azure und Canvas erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Canvas-Mandanten

Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie Canvas zugewiesen haben, mittels einmaligen Anmeldens auf der Canvas-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Sie können aber auch den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Canvas
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Szenario")
##Aktivieren der Anwendungsintegration für Canvas

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Canvas aktivieren.

###So aktivieren Sie die Anwendungsintegration für Canvas

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **Canvas** ein.

    ![Anwendungskatalog](./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Canvas** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Zeichenbereich](./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Zeichenbereich")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Canvas zu authentifizieren. Zum Konfigurieren des einmaligen Anmeldens für Canvas müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI) (in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Canvas** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Canvas anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Canvas-Anmelde-URL** die URL im Format `https://<tenant-name>.instructure.com` ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren um Canvas** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Canvas-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Courses > Managed Accounts > Microsoft**.

    ![Zeichenbereich](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Zeichenbereich")

7.  Wählen Sie im Navigationsbereich auf der linken Seite **Authentication** aus und klicken Sie dann auf **Add New SAML Config**.

    ![Authentifizierung](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Authentifizierung")

8.  Führen Sie auf der Seite "Current Integration" die folgenden Schritte aus.

    ![Aktuelle Integration](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Aktuelle Integration")

    1.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Canvas** den Wert der **Entitäts-ID**, und fügen Sie ihn in das Textfeld **IdP Entity ID** ein.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Canvas** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Log On URL** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Canvas** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Log Out URL** ein.
    4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Canvas** den Wert für **Kennwort-URL ändern**, und fügen Sie ihn in das Textfeld **Change Password Link** ein.
    5.  Kopieren Sie den **Fingerabdruckwert** aus dem exportierten Zertifikat, und fügen Sie ihn in das Textfeld **Certificate Fingerprint** ein.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI) (in englischer Sprache).

    6.  Wählen Sie in der Liste **Login Attribute** die Option **NameID** aus.
    7.  Wählen Sie in der Liste **Identifier Format** die Option **emailAddress** aus.
    8.  Klicken Sie auf **Save Authentication Settings**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Canvas anmelden können, müssen sie in Canvas bereitgestellt werden. Im Fall von Canvas ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Canvas**-Mandanten an.

2.  Wechseln Sie zu **Courses > Managed Accounts > Microsoft**.

    ![Zeichenbereich](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Zeichenbereich")

3.  Klicken Sie auf **Users**.

    ![Benutzer](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Benutzer")

4.  Klicken Sie auf **Add New User**.

    ![Benutzer](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Benutzer")

5.  Führen Sie auf der Dialogseite "Add a New User" die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Benutzer hinzufügen")

    1.  Geben Sie im Textfeld **Full Name** den Namen des Benutzers ein.
    2.  Geben Sie im Textfeld **Email** die E-Mail-Adresse des Benutzers ein.
    3.  Geben Sie im Textfeld **Login** die Azure AD-E-Mail-Adresse des Benutzers ein.
    4.  Aktivieren Sie **Email the user about this account creation**.
    5.  Klicken Sie auf **Add User**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe von anderen Tools zum Erstellen von Canvas-Benutzerkonten oder mithilfe der von Canvas bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Canvas Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Canvas** auf **Benutzer zuweisen**.

    ![Zuweisen von Benutzern](./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Zuweisen von Benutzern")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->