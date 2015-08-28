<properties pageTitle="Lernprogramm: Azure Active Directory-Integration mit Cisco Webex | Microsoft Azure" description="Erfahren Sie, wie Sie Cisco Webex mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Lernprogramm: Azure Active Directory-Integration mit Cisco Webex
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=524907), um Feedback abzugeben.

In diesem Lernprogramm wird die Integration von Azure und Cisco Webex erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Cisco Webex-Mandanten

Nach Abschluss dieses Lernprogramms können sich die Cisco Webex zugewiesenen Azure AD-Benutzer mittels einmaligen Anmeldens auf Ihrer Cisco Webex-Unternehmenswebsite bei der Anwendung anmelden \(durch den Dienstanbieter initiierte Anmeldung\). Sie können aber auch den Zugriffsbereich nutzen \(siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)\).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Cisco Webex
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Szenario")
##Aktivieren der Anwendungsintegration für Cisco Webex

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Cisco Webex aktivieren.

###So aktivieren Sie die Anwendungsintegration für Cisco Webex

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **Cisco Webex** ein.

    ![Anwendungskatalog](./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Cisco Webex** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Cisco Webex zu authentifizieren.
Im Rahmen dieses Verfahrens müssen Sie ein Base64-codiertes Zertifikat erstellen.
Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) \(in englischer Sprache\) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Cisco Webex** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Cisco Webex anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "Einmaliges Anmelden konfigurieren")

3.  Führen Sie auf der Seite **App-URL konfigurieren** die folgenden Schritte aus, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "App-URL konfigurieren")

    1.  Geben Sie im Textfeld **Anmelde-URL** Ihre Cisco Webex-Mandanten-URL ein \(z. B. **http://contoso.webex.com*).
2.  Geben Sie im Textfeld **Cisco Webex-Antwort-URL** Ihre **AssertionConsumerService-URL** für Cisco Webex ein \(z. B. **https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*).

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren um Cisco Webex** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Cisco Webex-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im oberen Menü auf **Site Administration**.

    ![Site Administration](./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Site Administration")

7.  Klicken Sie im Abschnitt **Manage Site** auf **SSO Configuration**.

    ![SSO Configuration](./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "SSO Configuration")

8.  Führen Sie im Abschnitt "Federated Web SSO Configuratio" die folgenden Schritte aus:

    ![Federated SSO Configuration](./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Federated SSO Configuration")

    1.  Wählen Sie in der Liste **Federation Protocol** die Option **SAML 2.0** aus.
    2.  Erstellen Sie eine **Base64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) \(in englischer Sprache\).

    3.  Öffnen Sie das Base64-codierte Zertifikat in Editor, und kopieren Sie seinen Inhalt.
    4.  Klicken Sie auf **Import SAML Metadata**, und fügen Sie dann das Base64-codierte Zertifikat ein.
    5.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Cisco Webex** den Wert für **Aussteller-URL**, und fügen Sie ihn in das Textfeld **Issuer for SAML \(IdP ID\)** ein.
    6.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Cisco Webex** den Wert für **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Customer SSO Service Login URL** ein.
    7.  Wählen Sie in der Liste **NameID Format** die Option **Email address** aus.
    8.  Geben Sie in das Textfeld **AuthnContextClassRef** die Zeichenkette **urn:oasis:names:tc:SAML:2.0:ac:classes:Password** ein.
    9.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren um Cisco Webex** den Wert für **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Customer SSO Service Logout URL** ein.
    10. Klicken Sie auf **Update**.

9.  Wählen Sie im Azure-Portal auf der Dialogseite **Einmaliges Anmelden konfigurieren um Cisco Webex** die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Cisco Webex anmelden können, müssen sie in Cisco Webex bereitgestellt werden. Im Fall von Cisco Webex ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Cisco Webex**-Mandanten an.

2.  Wechseln Sie zu **Manage Users \> Add User**.

    ![Hinzufügen von Benutzern](./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "Hinzufügen von Benutzern")

3.  Führen Sie im Abschnitt "Add User" die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Benutzer hinzufügen")

    1.  Wählen Sie unter **Account Type** die Option **Host** aus.
    2.  Geben Sie die Informationen eines vorhandenen Azure AD-Benutzers in die folgenden Textfelder ein:**First name, Last name**, **User name**, **Email**, **Password**, **Confirm Password**.
    3.  Klicken Sie auf **Hinzufügen**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe von anderen Tools zum Erstellen von Cisco Webex-Benutzerkonten oder mithilfe der von Cisco Webex bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Cisco Webex Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Cisco Webex** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!----HONumber=August15_HO7-->