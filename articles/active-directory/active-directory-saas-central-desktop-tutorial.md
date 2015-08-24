<properties pageTitle="Lernprogramm: Azure Active Directory-Integration mit Central Desktop | Microsoft Azure" description="Erfahren Sie, wie Sie Central Desktop mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Lernprogramm: Azure Active Directory-Integration mit Central Desktop
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=522411), um Feedback abzugeben.

In diesem Lernprogramm wird die Integration von Azure und Central Desktop erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Abonnement für Central Desktop mit aktiviertem einmaligen Anmelden und einen Central Desktop-Mandanten

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Central Desktop
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Szenario")
##Aktivieren der Anwendungsintegration für Central Desktop

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Central Desktop aktivieren.

###So aktivieren Sie die Anwendungsintegration für Central Desktop

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **Central Desktop** ein.

    ![Anwendungskatalog](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Central Desktop** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Central Desktop zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base64-codierte Zertifikatsdatei in Ihren Central Desktop-Mandanten hochladen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) \(in englischer Sprache\) weitere Informationen.

 

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Central Desktop** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Central Deskop anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Einmaliges Anmelden konfigurieren")

3.  Führen Sie auf der Seite **App-URL konfigurieren** die folgenden Schritte aus, und klicken Sie dann auf **Weiter**:

    -   Geben Sie im Textfeld**Central Desktop-Anmelde-URL** die URL Ihres Central Desktop-Mandanten ein \(z. B. **http://contoso.centraldesktop.com*).
-   Geben Sie im Textfeld "Central Desktop-Antwort-URL" Ihre Central Desktop AssertionConsumerService-URL ein \(z. B. https://contoso.centraldesktop.com/saml2-assertion.php).

    >[AZURE.NOTE]Sie finden den Wert in den Central Desktop-Metadaten \(z. B. **http://contoso.centraldesktop.com*).

    ![App-URL konfigurieren](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren um Central Desktop** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich bei Ihrem **Central Desktop**-Mandanten an.

6.  Wechseln Sie zu**Settings**, klicken Sie auf **Advanced**, und klicken Sie dann auf**Single Sign On**.

    ![Setup - Advanced](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Setup - Advanced")

7.  Führen Sie im Dialogfeld **Single Sign On Settings** die folgenden Schritte aus:

    ![Single Sign On Settings](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Single Sign On Settings")

    1.  Aktivieren Sie **Enable SAML v2 Single Sign On**.
    2.  Kopieren Sie im Azure AD-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Central Desktop** den Wert für **Aussteller-URL**, und fügen Sie ihn in das Textfeld **SSO URL** ein.
    3.  Kopieren Sie im Azure AD-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Central Desktop** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **SSO Login URL** ein.
    4.  Kopieren Sie im Azure AD-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Central Desktop** den Wert für **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **SSO Logout URL** ein.

8.  Führen Sie im Abschnitt **Message Signature Verification Method** die folgenden Schritte aus:

    ![Message Signature Verification Method](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Message Signature Verification Method")

    1.  Aktivieren Sie **Certificate**.
    2.  Wählen Sie in der Liste **SSO Certificate** die Option **RSH SHA256** aus.
    3.  Erstellen Sie eine Textdatei aus dem heruntergeladenen Zertifikat, kopieren Sie den Inhalt der Textdatei, und fügen Sie ihn in das Feld **SSO Certificate** ein.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) \(in englischer Sprache\).

    4.  Aktivieren Sie **Display a link to your SAMLv2 login page**.

9.  Klicken Sie auf **Update**.

10. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich AAD-Benutzer anmelden können, müssen sie in der Central Desktop-Anwendung bereitgestellt werden. In diesem Abschnitt wird beschrieben, wie in Central Desktop AAD-Benutzerkonten erstellt werden.

###So stellen Sie Benutzerkonten für Central Desktop bereit

1.  Melden Sie sich bei Ihrem Central Desktop-Mandanten an.

2.  Wechseln Sie zu **People \> Internal Members**.

3.  Klicken Sie auf **Add Internal Members**.

    ![People](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "People")

4.  Geben Sie im Textfeld **Email Address of New Members** ein AAD-Konto ein, das Sie bereitstellen möchten, und klicken Sie dann auf **Weiter**.

    ![Email Addresses of New Members](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Email Addresses of New Members")

5.  Klicken Sie auf **Add Internal member\(s\)**.

    ![Add Internal Member](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Add Internal Member")

    >[AZURE.NOTE]Die Benutzer, die Sie hinzugefügt haben, erhalten eine E-Mail mit einem Link zur Bestätigung, auf den sie zur Bestätigung klicken müssen.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Central Desktop-Benutzerkonten oder mithilfe der von Central Desktop bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Central Desktop Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Central Desktop** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->