<properties 
    pageTitle="Lernprogramm: Azure AD-Integration mit BlueJeans | Microsoft Azure" 
    description="Erfahren Sie, wie Sie BlueJeans mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Lernprogramm: Azure AD-Integration mit BlueJeans

In diesem Lernprogramm wird die Integration von Azure und BlueJeans erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein BlueJeans-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Tutorials können sich die BlueJeans zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer BlueJeans-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md) nutzen.

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für BlueJeans
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Szenario")
##Aktivieren der Anwendungsintegration für BlueJeans

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für BlueJeans aktivieren.

###So aktivieren Sie die Anwendungsintegration für BlueJeans

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **BlueJeans** ein.

    ![Anwendungskatalog](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **BlueJeans** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![BlueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei BlueJeans zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **BlueJeans** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei BlueJeans anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld **BlueJeans-Anmelde-URL** die URL im Format **https://company.BlueJeans.com*" ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren um BlueJeans** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der **BlueJeans**-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **ADMIN > Group Settings > Security**.

    ![Admin](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Admin")

7.  Führen Sie im Abschnitt **Sicherheit** die folgenden Schritte aus:

    ![Einmaliges Anmelden für SAML](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "Einmaliges Anmelden für SAML")

    1.  Wählen Sie **SAML Single Sign On** aus.
    2.  Wählen Sie **Enable automatic provisioning** aus.

8.  Führen Sie die folgenden Schritte aus:

    ![Zertifikatpfad](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Zertifikatpfad")

    1.  Klicken Sie auf **Datei auswählen**, und laden Sie dann das heruntergeladene Zertifikat hoch.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um BlueJeans** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Login URL** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um BlueJeans** den Wert für **Kennwort-URL ändern**, und fügen Sie ihn in das Textfeld **Password Change URL** ein.
    4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um BlueJeans** den Wert für **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Logout URL** ein.

9.  Führen Sie die folgenden Schritte aus:

    ![Änderungen speichern](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Änderungen speichern")

    1.  Geben Sie in das Textfeld **Benutzer-ID** den Wert ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name** ein.
2.  Geben Sie in das Textfeld **E-Mail** den Wert ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name** ein.
3.  Klicken Sie auf **Änderungen speichern**.

10. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei BlueJeans anmelden können, müssen sie in BlueJeans bereitgestellt werden. Im Fall von BlueJeans ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei der **BlueJeans**-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **ADMIN > Manage Users > Add User**.

    ![Admin](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Admin")

    >[AZURE.IMPORTANT]Die Registerkarte **Add User** ist nur verfügbar, wenn auf der Registerkarte **Security** die Option **Enable automatic provisioning** deaktiviert ist.

3.  Führen Sie im Abschnitt **Add User** die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Benutzer hinzufügen")

    1.  Geben Sie Werte für **BlueJeans Username**, **Email address**, **BlueJeans Meeting ID**, **Moderator Passcode**, **Full Name** und **Company** eines gültigen AAD-Kontos für die Bereitstellung in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Add User**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von BlueJeans-Benutzerkonten oder mithilfe von APIs erstellen, die von BlueJeans zum Bereitstellen von AAD-Benutzerkonten bereitgestellt werden.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie BlueJeans Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **BlueJeans** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->